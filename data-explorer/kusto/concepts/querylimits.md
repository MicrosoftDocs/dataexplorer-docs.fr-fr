---
title: Limites de requête-Azure Explorateur de données
description: Cet article décrit les limites de requête dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: cbdebe75713bb7cd786941e7546ab477df497c20
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512908"
---
# <a name="query-limits"></a>Limites de requête

Kusto est un moteur de requête ad hoc qui héberge des jeux de données volumineux et tente de répondre aux requêtes en conservant toutes les données pertinentes en mémoire.
Il existe un risque inhérent au fait que les requêtes monopolisent les ressources de service sans limites. Kusto fournit un certain nombre de protections intégrées sous la forme de limites de requête par défaut. Si vous envisagez de supprimer ces limites, déterminez d’abord si vous obtenez une valeur en procédant ainsi.

## <a name="limit-on-query-concurrency"></a>Limite de concurrence des requêtes

L' **accès concurrentiel aux requêtes** est une limite qu’un cluster impose à un certain nombre de requêtes exécutées en même temps.

* La valeur par défaut de la limite de concurrence des requêtes dépend du cluster SKU sur lequel elle s’exécute et est calculé comme suit : `Cores-Per-Node x 10` .
  * Par exemple, pour un cluster qui est configuré sur la référence SKU D14v2, où chaque ordinateur a 16 vCores, la limite de concurrence des requêtes par défaut est `16 cores x10 = 160` .
* La valeur par défaut peut être modifiée en configurant la [stratégie de limitation des requêtes](../management/query-throttling-policy.md). 
  * Le nombre réel de requêtes pouvant s’exécuter simultanément sur un cluster dépend de différents facteurs. Les facteurs les plus dominants sont la référence SKU de cluster, les ressources disponibles du cluster et les modèles de requête. La stratégie de limitation des requêtes peut être configurée en fonction des tests de charge effectués sur des modèles de requête de type production.

## <a name="limit-on-result-set-size-result-truncation"></a>Limite de la taille du jeu de résultats (troncation des résultats)

La **troncation des résultats** est une limite définie par défaut sur le jeu de résultats retourné par la requête. Kusto limite le nombre d’enregistrements renvoyés au client à **500 000**, et la taille globale des données pour ces enregistrements à **64 Mo**. Lorsque l’une ou l’autre de ces limites est dépassée, la requête échoue avec un « échec partiel de la requête ». Le dépassement de la taille globale des données génère une exception avec le message :

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Le dépassement du nombre d’enregistrements échouera avec une exception qui indique :

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Il existe plusieurs stratégies pour traiter cette erreur.

* Réduisez la taille du jeu de résultats en modifiant la requête pour qu’elle retourne uniquement des données intéressantes. Cette stratégie est utile lorsque la requête initiale ayant échoué est trop « grande ». Par exemple, la requête ne projette pas les colonnes de données qui ne sont pas nécessaires.
* Réduisez la taille du jeu de résultats en décalant le traitement après requête, tel que les agrégations, dans la requête elle-même. La stratégie est utile dans les scénarios où la sortie de la requête est acheminée vers un autre système de traitement, et qui effectue ensuite des agrégations supplémentaires.
* Basculez des requêtes vers à l’aide de l' [exportation de données](../management/data-export/index.md) lorsque vous souhaitez exporter de grands ensembles de données à partir du service.
* Demandez au service de supprimer cette limite de requête à l’aide `set` des instructions listées ci-dessous ou des indicateurs dans les [Propriétés de demande du client](../api/netfx/request-properties.md).

Les méthodes de réduction de la taille du jeu de résultats produite par la requête sont les suivantes :

* Utilisez le groupe d' [opérateurs de synthèse](../query/summarizeoperator.md) et regroupez les enregistrements similaires dans le résultat de la requête. Échantillonnez éventuellement des colonnes à l’aide de la [fonction d’agrégation any](../query/any-aggfunction.md).
* Utilisez un [opérateur Take](../query/takeoperator.md) pour échantillonner le résultat de la requête.
* Utilisez la [fonction SUBSTRING](../query/substringfunction.md) pour supprimer les colonnes de texte libre larges.
* Utilisez l' [opérateur de projet](../query/projectoperator.md) pour supprimer toute colonne inintéressante du jeu de résultats.

Vous pouvez désactiver la troncation des résultats à l’aide de l' `notruncation` option de demande.
Nous vous recommandons de mettre en place une certaine forme de limitation.

Par exemple :

```kusto
set notruncation;
MyTable | take 1000000
```

Il est également possible de disposer d’un contrôle plus affiné sur la troncation des résultats en définissant la valeur de `truncationmaxsize` (taille maximale des données, en octets, 64 Mo par défaut) et `truncationmaxrecords` (nombre maximal d’enregistrements, 500 000 par défaut). Par exemple, la requête suivante définit la troncation des résultats sur 1 105 enregistrements ou 1 Mo, selon la valeur dépassée.

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="UserId1"
```

La suppression de la limite de troncation des résultats signifie que vous avez l’intention de déplacer les données en bloc en dehors de Kusto.

Vous pouvez supprimer la limite de troncation des résultats à des fins d’exportation à l’aide de la `.export` commande ou d’une agrégation ultérieure. Si vous choisissez l’agrégation ultérieure, envisagez l’agrégation à l’aide de Kusto.

Kusto fournit un certain nombre de bibliothèques clientes qui peuvent gérer des résultats « infiniment volumineux » en les diffusant à l’appelant. Utilisez l’une de ces bibliothèques et configurez-la en mode de diffusion en continu. Par exemple, utilisez le client .NET Framework (Microsoft. Azure. Kusto. Data) et affectez à la propriété streaming de la chaîne de connexion la *valeur true*, ou utilisez l’appel *ExecuteQueryV2Async ()* qui transmet toujours les résultats.

La troncation des résultats est appliquée par défaut, pas uniquement au flux de résultats retourné au client. Elle est également appliquée par défaut aux sous-requêtes qu’un cluster émet sur un autre cluster dans une requête entre clusters, avec des effets similaires.

## <a name="limit-on-memory-per-iterator"></a>Limite de mémoire par itérateur

La **mémoire maximale par itérateur de jeu de résultats** est une autre limite utilisée par Kusto pour la protection contre les requêtes « inversées ». Cette limite, représentée par l’option de demande `maxmemoryconsumptionperiterator` , définit une limite supérieure de la quantité de mémoire qu’un itérateur de jeu de résultats de plan de requête unique peut contenir. Cette limite s’applique aux itérateurs spécifiques qui ne sont pas diffusés par nature, tels que `join` .) Voici quelques messages d’erreur qui seront renvoyés lorsque cette situation se produit :

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

Dans de nombreux cas, le dépassement de cette limite peut être évité en échantillonnant le jeu de données. Les deux requêtes ci-dessous montrent comment effectuer l’échantillonnage. La première est un échantillonnage statistique, qui utilise un générateur de nombres aléatoires. Le second est un échantillonnage déterministe, effectué par le hachage d’une colonne du jeu de données, généralement un ID.

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>Limite de mémoire par nœud

La **mémoire maximale par requête par nœud** est une autre limite utilisée pour la protection contre les requêtes « inversées ». Cette limite, représentée par l’option de demande `max_memory_consumption_per_query_per_node` , définit une limite supérieure de la quantité de mémoire qui peut être utilisée sur un seul nœud pour une requête spécifique.

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>Limite sur les ensembles de chaînes accumulés

Dans diverses opérations de requête, Kusto doit « rassembler » des valeurs de chaîne et les mettre en mémoire tampon avant de commencer à produire des résultats. Ces jeux de chaînes accumulés sont limités en taille et dans le nombre d’éléments qu’ils peuvent contenir. En outre, chaque chaîne individuelle ne doit pas dépasser une certaine limite.
Si vous dépassez l’une de ces limites, l’une des erreurs suivantes se produit :

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of ..GB items (see http://aka.ms/kustoquerylimits)')
```

Il n’existe actuellement aucun commutateur pour augmenter la taille maximale du jeu de chaînes.
En guise de solution de contournement, reformulez la requête pour réduire la quantité de données qui doivent être mises en mémoire tampon. Vous pouvez projeter des colonnes inutiles avant qu’elles ne soient utilisées par des opérateurs tels que Join et Resume. Vous pouvez utiliser la stratégie de [requête de permutation](../query/shufflequery.md) .

## <a name="limit-execution-timeout"></a>Limiter le délai d’exécution

Le **délai d’attente du serveur** est un délai d’expiration côté service qui est appliqué à toutes les demandes.
Le délai d’expiration des requêtes en cours d’exécution (requêtes et commandes de contrôle) est appliqué à plusieurs points dans le Kusto :

* bibliothèque cliente (si utilisée)
* point de terminaison de service qui accepte la demande
* moteur de service qui traite la requête

Par défaut, le délai d’attente est défini sur quatre minutes pour les requêtes et sur 10 minutes pour les commandes de contrôle. Cette valeur peut être augmentée si nécessaire (limité à une heure).

* Si vous interrogez à l’aide de Kusto. Explorer, utilisez options des **Outils** &gt; **Options** _ &gt; _ *connexions* *  &gt; **requête du serveur**.
* Par programmation, définissez la `servertimeout` propriété demande du client, une valeur de type `System.TimeSpan` , jusqu’à une heure.

**Remarques sur les délais d’expiration**

* Côté client, le délai d’attente est appliqué à partir de la demande en cours de création jusqu’au moment où la réponse commence à arriver au client. Le temps nécessaire à la lecture de la charge utile sur le client n’est pas traité dans le cadre du délai d’attente. Cela dépend de la vitesse à laquelle l’appelant extrait les données du flux.
* De même, côté client, la valeur de délai d’expiration réelle utilisée est légèrement supérieure à la valeur de délai d’attente du serveur demandée par l’utilisateur. Cette différence, consiste à autoriser les latences du réseau.
* Pour utiliser automatiquement le délai d’expiration maximal des demandes autorisées, affectez à la propriété demande du client la valeur `norequesttimeout` `true` .

## <a name="limit-on-query-cpu-resource-usage"></a>Limite sur l’utilisation des ressources processeur des requêtes

Kusto vous permet d’exécuter des requêtes et d’utiliser autant de ressources processeur que le cluster. Elle tente d’effectuer un juste tourniquet (Round Robin) entre les requêtes si plusieurs sont en cours d’exécution. Cette méthode produit les meilleures performances pour les requêtes ad hoc.
À d’autres moments, vous souhaiterez peut-être limiter les ressources processeur utilisées pour une requête particulière. Par exemple, si vous exécutez une tâche en arrière-plan, le système peut tolérer des latences plus élevées pour fournir une priorité élevée aux requêtes ad hoc simultanées.

Kusto prend en charge la spécification de deux [Propriétés de demande client](../api/netfx/request-properties.md) lors de l’exécution d’une requête. Les propriétés sont  *query_fanout_threads_percent* et *query_fanout_nodes_percent*.
Les deux propriétés sont des entiers dont la valeur par défaut est la valeur maximale (100), mais elle peut être réduite pour une requête spécifique à une autre valeur. 

La première, *query_fanout_threads_percent*, contrôle le facteur de Fanout pour l’utilisation des threads. Quand il est de 100%, le cluster affecte tous les processeurs sur chaque nœud. Par exemple, 16 UC sur un cluster déployé sur des nœuds Azure D14. Au 50%, la moitié des UC sera utilisée, et ainsi de suite. Les nombres sont arrondis à un processeur entier, donc il est possible de le définir sur 0. Le deuxième, *query_fanout_nodes_percent*, contrôle le nombre de nœuds de requête du cluster à utiliser par opération de distribution de sous-requête. Elle fonctionne de façon similaire.

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

Pour ce cas spécifique, réécrivez la requête à l’aide de l' [`in()`](../query/inoperator.md) opérateur.

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```
