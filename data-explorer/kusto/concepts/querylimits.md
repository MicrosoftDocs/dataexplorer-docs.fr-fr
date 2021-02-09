---
title: Limites de requête - Azure Data Explorer
description: Cet article décrit les limites de requête dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: 615b2f681c22237f9d14ad92e285a564c249857e
ms.sourcegitcommit: d1c2433df183d0cfbfae4d3b869ee7f9cbf00fe4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99586371"
---
# <a name="query-limits"></a>Limites de requête

Kusto est un moteur de requête ad hoc qui héberge des jeux de données volumineux et tente de répondre aux requêtes en conservant toutes les données pertinentes en mémoire.
Il existe un risque inhérent au fait que les requêtes monopolisent les ressources de service sans limites. Kusto fournit plusieurs protections intégrées sous la forme de limites de requête par défaut. Si vous envisagez de supprimer ces limites, déterminez d’abord si cela constitue un réel avantage.

## <a name="limit-on-request-concurrency"></a>Limite sur la concurrence des demandes

La **concurrence des demandes** est une limite qu’un cluster impose sur plusieurs demandes exécutées en même temps.

* La valeur par défaut de la limite dépend de la référence SKU sur laquelle le cluster s’exécute, et est calculée comme suit : `Cores-Per-Node x 10`.
  * Par exemple, pour un cluster qui est configuré sur la référence SKU D14v2, où chaque machine a 16 vCores, la limite par défaut est de `16 cores x10 = 160`.
* La valeur par défaut peut être modifiée en configurant la [stratégie de limites de taux de demandes](../management/request-rate-limit-policy.md) du groupe de charge de travail `default`.
  * Le nombre réel de demandes pouvant s’exécuter simultanément sur un cluster dépend de différents facteurs. Les facteurs les plus dominants sont la référence SKU du cluster, les ressources disponibles du cluster et les modèles d’usage. La stratégie peut être configurée en fonction des tests de charge effectués sur des modèles d’usage de type production.

## <a name="limit-on-result-set-size-result-truncation"></a>Limite de la taille du jeu de résultats (troncation des résultats)

La **troncation des résultats** est une limite définie par défaut sur le jeu de résultats retourné par la requête. Kusto limite le nombre d’enregistrements retournés au client à **500 000** et la taille globale des données pour ces enregistrements à **64 Mo**. Lorsque l’une ou l’autre de ces limites est dépassée, la requête échoue avec un « échec partiel de la requête ». Le dépassement de la taille globale des données génère une exception avec le message suivant :

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Le dépassement du nombre d’enregistrements aboutit à un échec avec une exception qui indique :

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Il existe plusieurs stratégies pour gérer cette erreur.

* Réduisez la taille du jeu de résultats en modifiant la requête pour qu’elle retourne uniquement des données intéressantes. Cette stratégie est utile lorsque la requête initiale ayant échoué est trop « large ». Par exemple, la requête ne projette pas les colonnes de données qui ne sont pas nécessaires.
* Réduisez la taille du jeu de résultats en décalant le traitement après requête, tel que les agrégations, dans la requête elle-même. La stratégie est utile dans les scénarios où la sortie de la requête est acheminée vers un autre système de traitement, qui effectue ensuite d’autres agrégations.
* Basculez des requêtes à l’utilisation de l’[exportation de données](../management/data-export/index.md) lorsque vous souhaitez exporter de grands jeux de données à partir du service.
* Demandez au service de supprimer cette limite de requête à l’aide des instructions `set` listées ci-dessous ou des indicateurs disponibles dans les [propriétés de demande du client](../api/netfx/request-properties.md).

Les méthodes de réduction de la taille de jeu de résultats produite par la requête sont les suivantes :

* Utilisez le groupe d’[opérateurs summarize](../query/summarizeoperator.md) et agrégez des enregistrements similaires dans le résultat de la requête. Échantillonnez éventuellement des colonnes à l’aide d’une [fonction d’agrégation](../query/any-aggfunction.md).
* Utilisez un [opérateur take](../query/takeoperator.md) pour échantillonner le résultat de la requête.
* Utilisez la [fonction substring](../query/substringfunction.md) pour supprimer les grandes colonnes de texte libre.
* Utilisez l’[opérateur project](../query/projectoperator.md) pour supprimer toute colonne inintéressante du jeu de résultats.

Vous pouvez désactiver la troncation des résultats à l’aide de l’option de demande `notruncation`.
Nous vous recommandons de continuer à mettre en place une certaine forme de limitation.

Par exemple :

```kusto
set notruncation;
MyTable | take 1000000
```

Il est également possible d’avoir un contrôle plus fin sur la troncation des résultats en définissant la valeur de `truncationmaxsize` (taille de données maximale en octets, 64 Mo par défaut) et `truncationmaxrecords` (nombre maximal d’enregistrements, 500 000 par défaut). Par exemple, la requête suivante définit la troncation des résultats sur 1 105 enregistrements ou 1 Mo, selon la valeur dépassée.

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="UserId1"
```

Si vous supprimez la limite de troncation des résultats, cela signifie que vous avez l’intention de déplacer les données en bloc en dehors de Kusto.

Vous pouvez supprimer la limite de troncation des résultats à des fins d’exportation à l’aide de la commande `.export` ou d’une agrégation ultérieure. Si vous choisissez l’agrégation ultérieure, envisagez l’agrégation à l’aide de Kusto.

Kusto fournit un certain nombre de bibliothèques clientes qui peuvent gérer des résultats « infiniment volumineux » en les diffusant à l’appelant. Utilisez l’une de ces bibliothèques et configurez-la en mode de streaming. Par exemple, utilisez le client .NET Framework (Microsoft.Azure.Kusto.Data) et définissez la propriété de streaming de la chaîne de connexion sur *true*, ou utilisez l’appel *ExecuteQueryV2Async()* qui transmet toujours les résultats.

La troncation des résultats est appliquée par défaut, pas uniquement au flux de résultats retourné au client. Elle est également appliquée par défaut aux sous-requêtes qu’un cluster émet sur un autre cluster dans une requête entre clusters, avec des effets similaires.

### <a name="setting-multiple-result-truncation-properties"></a>Définition de plusieurs propriétés de troncation de résultats

Les éléments suivants s’appliquent lors de l’utilisation d’instructions `set` et/ou lors de la spécification d’indicateurs dans les [propriétés de demande du client](../api/netfx/request-properties.md).

* Si `notruncation` est défini et que `truncationmaxsize`, `truncationmaxrecords` ou `query_take_max_records` sont aussi définis - `notruncation` est ignoré.
* Si `truncationmaxsize`, `truncationmaxrecords` et/ou `query_take_max_records` sont définis plusieurs fois, la valeur *inférieure* de chaque propriété s’applique.

## <a name="limit-on-memory-per-iterator"></a>Limite de mémoire par itérateur

La **mémoire maximale par itérateur de jeu de résultats** est une autre limite utilisée par Kusto pour la protection contre les pertes de contrôle de requête. Cette limite, représentée par l’option de demande `maxmemoryconsumptionperiterator`, définit une limite supérieure sur la quantité de mémoire qu’un seul itérateur de jeu de résultats de plan de requête peut contenir. Cette limite s’applique aux itérateurs spécifiques qui ne sont pas diffusés par nature, par exemple `join`.) Voici quelques messages d’erreur qui sont retournés lorsque cette situation se produit :

```
The ClusterBy operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete E_RUNAWAY_QUERY.

The DemultiplexedResultSetCache operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The ExecuteAndCache operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The Sort operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The Summarize operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The TopNestedAggregator operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The TopNested operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).
```

Par défaut, cette valeur est définie sur 5 Go. Vous pouvez augmenter cette valeur jusqu’à la moitié de la mémoire physique de l’ordinateur :

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

Dans de nombreux cas, le dépassement de cette limite peut être évité en échantillonnant le jeu de données. Les deux requêtes ci-dessous montrent comment effectuer l’échantillonnage. La première est un échantillonnage statistique qui utilise un générateur de nombres aléatoires. La seconde est un échantillonnage déterministe, effectué par le hachage d’une colonne du jeu de données, généralement un ID.

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

Si `maxmemoryconsumptionperiterator` est défini plusieurs fois, par exemple dans les propriétés de demande client et en utilisant une instruction `set`, la valeur inférieure est appliquée.


## <a name="limit-on-memory-per-node"></a>Limite de mémoire par nœud

La **mémoire maximale par requête par nœud** est une autre limite utilisée pour la protection contre les pertes de contrôle de requête. Cette limite, représentée par l’option de demande `max_memory_consumption_per_query_per_node`, définit une limite supérieure sur la quantité de mémoire qui peut être utilisée sur un seul nœud pour une requête spécifique.

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

Si `max_memory_consumption_per_query_per_node` est défini plusieurs fois, par exemple dans les propriétés de demande client et en utilisant une instruction `set`, la valeur inférieure est appliquée.

## <a name="limit-on-accumulated-string-sets"></a>Limite sur les ensembles de chaînes accumulés

Dans diverses opérations de requête, Kusto doit « rassembler » des valeurs de chaîne et les mettre en mémoire tampon en interne avant de commencer à produire des résultats. Ces jeux de chaînes accumulés sont limités en taille et dans le nombre d’éléments qu’ils peuvent contenir. En outre, chaque chaîne individuelle ne doit pas dépasser une certaine limite.
Si vous dépassez l’une de ces limites, l’une des erreurs suivantes se produit :

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of ..GB items (see http://aka.ms/kustoquerylimits)')
```

Il n’existe actuellement aucun commutateur pour augmenter la taille maximale du jeu de chaînes.
En guise de solution de contournement, reformulez la requête pour réduire la quantité de données qui doivent être mises en mémoire tampon. Vous pouvez rejeter des colonnes inutiles avant qu’elles ne soient utilisées par des opérateurs tels que join et summarize. Ou vous pouvez utiliser la stratégie de [requête aléatoire](../query/shufflequery.md).

## <a name="limit-execution-timeout"></a>Limiter l’expiration de l’exécution

Le **délai d’expiration du serveur** est un délai d’attente côté service qui est appliqué à toutes les demandes.
Le délai d’expiration des requêtes en cours d’exécution (requêtes et commandes de contrôle) est appliqué à plusieurs points dans les composants suivants de Kusto :

* Bibliothèque cliente (si utilisée)
* Point de terminaison de service qui accepte la demande
* Moteur de service qui traite la demande

Par défaut, le délai d’expiration est défini sur quatre minutes pour les requêtes et sur 10 minutes pour les commandes de contrôle. Cette valeur peut être augmentée si nécessaire (elle est limitée à une heure).

* Si vous effectuez des requêtes avec Kusto.Explorer, utilisez **Outils** &gt; **Options*** &gt; **Connexions** &gt; **Délai du serveur de requêtes**.
* Par programmation, définissez la propriété de demande de client `servertimeout`, une valeur de type `System.TimeSpan`, jusqu’à une heure.

**Remarques sur les délais d’expiration**

* Côté client, le délai d’expiration est appliqué à partir de la création de la demande jusqu’au moment où la réponse commence à arriver au client. Le temps nécessaire à la lecture de la charge utile sur le client n’est pas pris en compte dans le délai d’expiration. Il dépend de la vitesse à laquelle l’appelant extrait les données du flux.
* De même, côté client, la valeur de délai d’expiration réelle utilisée est légèrement supérieure à la valeur de délai d’expiration du serveur demandée par l’utilisateur. Cette différence consiste à autoriser les latences du réseau.
* Pour utiliser automatiquement le délai d’expiration de demande maximal autorisé, définissez la propriété de demande du client `norequesttimeout` sur `true`.

## <a name="limit-on-query-cpu-resource-usage"></a>Limite sur l’utilisation des ressources processeur des requêtes

Kusto vous permet d’exécuter des requêtes et d’utiliser autant de ressources processeur que le cluster. Il tente d’effectuer un aller-retour (round robin) juste entre les requêtes si plusieurs sont en cours d’exécution. Cette méthode produit les meilleures performances pour les requêtes ad hoc.
À d’autres moments, vous avez la possibilité de limiter les ressources processeur utilisées pour une requête particulière. Par exemple, si vous exécutez une tâche en arrière-plan, le système peut tolérer des latences plus élevées pour fournir une priorité élevée aux requêtes ad hoc simultanées.

Kusto prend en charge la spécification de deux [propriétés de demande de client](../api/netfx/request-properties.md) lors de l’exécution d’une requête.
Les propriétés sont *query_fanout_threads_percent* et *query_fanout_nodes_percent*.
Les deux propriétés sont des entiers dont la valeur par défaut est la valeur maximale (100), qui peut être réduite pour une requête spécifique à une autre valeur.

La première propriété, *query_fanout_threads_percent*, contrôle le facteur de fanout pour l’utilisation des threads.
Quand cette propriété est définie sur 100 %, le cluster affecte tous les processeurs sur chaque nœud. Par exemple, 16 UC sur un cluster déployé sur des nœuds Azure D14.
Quand cette propriété est définie sur 50 %, la moitié des processeurs est utilisée, et ainsi de suite.
Les nombres sont arrondis à un processeur entier.Il est donc possible de définir la valeur de propriété sur 0.

La deuxième propriété, *query_fanout_nodes_percent*, contrôle le nombre de nœuds de requête du cluster à utiliser par opération de distribution de sous-requête.
Elle fonctionne de façon similaire.

Si `query_fanout_nodes_percent` ou `query_fanout_threads_percent` sont définis plusieurs fois, par exemple dans les propriétés de demande client et en utilisant une instruction `set`, la valeur inférieure pour chaque propriété est appliquée.

## <a name="limit-on-query-complexity"></a>Limite de complexité des requêtes

Pendant l’exécution de la requête, le texte de la requête est transformé en une arborescence d’opérateurs relationnels représentant la requête.
Si la profondeur de l’arborescence dépasse un seuil interne de plusieurs milliers de niveaux, la requête est considérée comme trop complexe pour le traitement et échoue avec un code d’erreur. L’échec indique que l’arborescence des opérateurs relationnels dépasse ses limites.
Les limites sont dépassées en raison de requêtes avec de longues listes d’opérateurs binaires chaînés ensemble. Par exemple :

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

Pour ce cas spécifique, réécrivez la requête à l’aide de l’opérateur [`in()`](../query/inoperator.md).

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```
