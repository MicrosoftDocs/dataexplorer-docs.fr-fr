---
title: Limites de requête - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les limites de requête dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 437e9781b9e29db7496292ba6a416f6e0c769e8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523067"
---
# <a name="query-limits"></a>Limites de requête

Étant donné que Kusto est un moteur de requête ad hoc qui héberge de grands ensembles de données et tente de répondre aux questions en tenant toutes les données pertinentes en mémoire, il existe un risque inhérent que les requêtes monopolisent les ressources de service sans limites. Kusto fournit un certain nombre de protections intégrées sous la forme de limites de requête par défaut.

## <a name="limit-on-query-concurrency"></a>Limite de la concordance des requêtes

**La concordance de requête** est une limite qu’un cluster impose à un certain nombre de requêtes en cours d’exécution en même temps.
La valeur par défaut de la limite de concordance de requête dépend du cluster `Cores-Per-Node x 10`SKU sur qui il fonctionne, et est calculée comme : . Par exemple, pour un cluster qui est configuré sur D14v2 SKU, où chaque machine a 16 `16 cores x10 = 160`vCores, la limite par défaut Query Concurrency sera .
La valeur par défaut peut être modifiée en créant un billet de support. À l’avenir, ce contrôle sera également exposé par le biais d’une commande de contrôle.

## <a name="limit-on-result-set-size-result-truncation"></a>Limite sur la taille de l’ensemble de résultat (troncation de résultat)

**La troncation de résultat** est une limite définie par défaut sur l’ensemble de résultat retourné par la requête. Kusto limite le nombre d’enregistrements retournés au client à **500 000**, et la mémoire globale de ces enregistrements à **64 Mo**. Lorsque l’une ou l’autre de ces limites est dépassée, la requête échoue avec une « défaillance partielle de la requête ». Dépasser la mémoire globale générera une exception avec le message :

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Le dépassement du nombre d’enregistrements échouera à une exception qui dit :

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

Il existe un certain nombre de stratégies pour faire face à cette erreur :

* Réduire la taille de l’ensemble de résultat en modifiant la requête pour ne pas retourner les données inintéressantes. Ceci est généralement utile lorsque la requête initiale (défaillante) est trop "large" (par exemple, elle ne projette pas les colonnes de données qui ne sont pas nécessaires.)
* Réduire la taille de l’ensemble de résultat en déplaçant le traitement post-requête (comme les agrégations) dans la requête elle-même. Ceci est utile dans les scénarios où la sortie de la requête est alimentée à un autre système de traitement qui effectue ensuite des agrégations supplémentaires. 
* Passer des requêtes à l’exportation de [données](../management/data-export/index.md).
   Ceci est approprié lorsque vous souhaitez exporter de grands ensembles de données du service.
* Instruire le service de supprimer cette limite de requête.

Les méthodes courantes pour réduire la taille de l’ensemble de résultat produite par la requête sont les :

* À l’aide du groupe [d’opérateurs de résumé](../query/summarizeoperator.md) et de l’agrégation sur des enregistrements similaires dans la sortie de requête, éventuellement l’échantillonnage de certaines colonnes à l’aide [de toute fonction d’agrégation](../query/any-aggfunction.md).
* Utilisation [d’un opérateur](../query/takeoperator.md) de prise pour échantillonner la sortie de requête.
* Utilisation de la [fonction de sous-corde](../query/substringfunction.md) pour couper de larges colonnes de texte libre.
* Utilisation de [l’opérateur](../query/projectoperator.md) du projet pour laisser tomber toute colonne inintéressante de l’ensemble de résultats.

On peut désactiver la troncation `notruncation` des résultats en utilisant l’option de demande. Il est fortement recommandé que, dans ce cas, une certaine forme de limitation soit encore mise en place. Par exemple :

```kusto
set notruncation;
MyTable | take 1000000
```

Il est également possible d’avoir un contrôle plus raffiné `truncationmaxsize` sur la troncation des résultats en fixant `truncationmaxrecords` la valeur de (taille maximale des données dans les octets, défauts à 64 Mo) et (nombre maximum d’enregistrements, défauts à 500 000). Par exemple, la requête suivante établit la troncation des résultats pour se produire à 1 105 enregistrements ou 1 Mo, selon le plus que l’on dépasse :

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="Ploni"
```

Les bibliothèques clientes de Kusto assument actuellement l’existence de cette limite. Bien que vous puissiez augmenter la limite sans limites, vous finirez par atteindre les limites des clients qui ne sont actuellement pas configurables. Une solution de contournement possible est de programmer directement contre le contrat REST API et de mettre en œuvre un analyseur de streaming pour les résultats de la requête Kusto. Faites savoir à l’équipe Kusto si vous rencontrez ce problème afin que nous puissions prioriser un client en streaming de manière appropriée.

La troncation de résultat est appliquée par défaut, et pas seulement au flux de résultat retourné au client; il est également appliqué par défaut à toute sous-requête qu’un Kusto a des problèmes de cluster à un autre cluster Kusto dans une requête à clusters croisés, avec des effets similaires.

## <a name="limit-on-memory-per-iterator"></a>Limite de la mémoire par itérateur

**La mémoire maximale par itérateur de jeu de résultat** est une autre limite utilisée par Kusto pour se protéger contre les requêtes « emballement ». Cette limite (représentée par `maxmemoryconsumptionperiterator`l’option de demande) fixe une limite supérieure sur la quantité de mémoire qu’un seul itérateur de résultat de plan de requête peut tenir. (Cette limite s’applique aux itérateurs spécifiques qui `join`ne sont pas en streaming par nature, tels que .) Voici quelques messages d’erreur qui seront retournés lorsque cela se produit :

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

Par défaut, cette valeur est réglée à 5 Go. On peut augmenter cette valeur jusqu’à la moitié de la mémoire physique de la machine:

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

Lorsque vous envisagez de supprimer ces limites, déterminez d’abord si vous gagnez réellement une valeur en le faisant. En particulier, la suppression de la limite de troncation des résultats signifie que vous avez l’intention de déplacer `.export` les données en vrac de Kusto - soit à des fins d’exportation (dans ce cas, vous êtes invité à utiliser la commande à la place), ou pour faire l’agrégation ultérieure (dans ce cas, envisager d’agrégation en utilisant Kusto).
Faites savoir à l’équipe Kusto si vous avez un scénario d’affaires qui ne peut être atteint par l’une ou l’autre de ces solutions suggérées.  

Dans de nombreux cas, le dépassement de cette limite peut être évité en échantillonnant les données définies à 10 %. Les deux requêtes ci-dessous montrent comment effectuer cet échantillonnage. Le premier est un échantillonnage statistique (à l’aide d’un générateur de nombres aléatoires). Le second est l’échantillonnage déterministe (en hachant une colonne de l’ensemble de données, généralement une certaine ID):

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>Limite de la mémoire par nœud

**La mémoire maximale par requête par nœud** est une autre limite utilisée par Kusto pour se protéger contre les requêtes « fugues ». Cette limite (représentée par `max_memory_consumption_per_query_per_node`l’option de demande) fixe une limite supérieure sur la quantité de mémoire qui peut être allouée sur un seul nœud pour une requête spécifique. 


```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>Limite sur les ensembles de cordes accumulés

Dans diverses opérations de requête, Kusto doit « rassembler » les valeurs des chaînes et les tamponner en interne avant de pouvoir commencer à produire des résultats. Ces ensembles de cordes accumulés sont de taille limitée et dans le nombre d’articles qu’ils peuvent contenir. En outre, chaque chaîne individuelle ne peut pas dépasser une certaine limite.
Le dépassement d’une de ces limites entraînera l’une des erreurs suivantes :

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of 2G items (see http://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Single string size shouldn't exceed the limit of 2GB (see http://aka.ms/kustoquerylimits)')
```

Il n’y a actuellement aucun commutateur pour augmenter la taille maximale de l’ensemble de chaîne.
En tant que solution de contournement, reformulez la requête pour réduire la quantité de données qui doivent être tamponnées. Par exemple, en projetant des colonnes non éteignantes avant qu’elles « entrent » dans des opérateurs tels que l’adhésion et la résumé. Ou, par exemple, en utilisant la stratégie [de requête shuffle.](../query/shufflequery.md)

## <a name="limit-on-request-execution-time-timeout"></a>Limite du temps d’exécution des demandes (délai d’attente)

**Le délai d’attente du** serveur est un délai d’attente côté service qui est appliqué à toutes les demandes.
Le délai d’exécution des demandes (requêtes et commandes de contrôle) est appliqué à plusieurs points :

* Dans la bibliothèque cliente de Kusto (si elle est utilisée)
* Dans le critère de service Kusto qui accepte la demande
* Dans le moteur de service Kusto qui traite la demande

Par défaut, le délai d’attente est réglé à quatre minutes pour les requêtes et dix minutes pour les commandes de contrôle. Cette valeur peut être augmentée si nécessaire (plafonnée à une heure) :

* Si vous interrogez à l’aide de Kusto.Explorer, utilisez **Tools** &gt; **Options** *  &gt; **Connections** &gt; **Query Server Timeout**.
* Sur le plan `servertimeout` programmatique, définissez la `System.TimeSpan`propriété de demande du client (une valeur de type, jusqu’à une heure).

Notes sur les délais d’attente:

* Du côté du client, le délai d’attente est appliqué à partir de la demande créée jusqu’au moment où la réponse commence à arriver au client. Le temps qu’il faut pour lire la charge utile du client n’est pas traité dans le cadre du délai d’attente (parce que cela dépend de la rapidité avec laquelle l’appelant tire les données du flux).
* Également du côté du client, la valeur réelle de délai d’attente utilisée est légèrement supérieure à la valeur de temps d’arrêt du serveur demandée par l’utilisateur. Il s’agit de permettre des retards réseau.
* Du côté du service, tous les opérateurs de requêtes n’honorent pas la valeur du délai d’attente.
   Nous ajoutons graduellement ce soutien.
* Pour que Kusto utilise automatiquement le délai d’attente `norequesttimeout` `true`maximal autorisé pour la demande, définissez la propriété de demande du client à .

<!--
  Request timeout can also be set using a set statement, but we don't mention
  it here as it should not be used in production scenarios.
-->

## <a name="limit-on-query-cpu-resource-usage"></a>Limite de l’utilisation des ressources CPU

Par défaut, Kusto permet aux requêtes en cours d’exécution d’utiliser autant de ressources CPU que le cluster a, en essayant de faire un fair round-robin entre les requêtes si plus d’un est en cours d’exécution. Dans de nombreux cas, cela donne la meilleure performance pour les requêtes ad hoc.
Dans d’autres cas, on pourrait vouloir limiter les ressources CPU allouées pour une requête particulière. Par exemple, si l’on est en cours d’exécution d’un «travail de fond», on pourrait tolérer des retards plus élevés afin de donner des requêtes ad hoc concurrente haute priorité.

Kusto support spécifier deux [propriétés de demande de client](../api/netfx/request-properties.md) lors de l’exécution d’une requête, **query_fanout_threads_percent** et **query_fanout_nodes_percent**.
Les deux sont des intégrants qui ne sont pas à la valeur maximale (100), mais peuvent être réduits pour une requête spécifique à une certaine valeur. Le premier contrôle le facteur fanout pour l’utilisation du thread; lorsqu’il est à 100%, le cluster assignera tous les processeurs sur chaque nœud (par exemple, sur un cluster déployé sur les nœuds Azure D14, 16 processeurs) à la requête, quand il est de 50% que la moitié des processeurs seront utilisés, etc . (Les numéros sont arrondis à un processeur entier, il est donc sûr de le régler à 0.) Le second contrôle le nombre de nœuds dans le cluster à utiliser par opération de distribution de sous-requête, et fonctionne d’une manière similaire.

## <a name="limit-on-query-complexity"></a>Limite de la complexité des requêtes

Pendant l’exécution des requêtes, le texte de requête est transformé en arbre d’opérateurs relationnels représentant la requête.
Dans le cas où la profondeur de l’arbre dépasse un seuil interne (plusieurs milliers de niveaux), la requête est considérée comme trop complexe pour le traitement et échouera avec un code d’erreur indiquant que l’arbre des opérateurs relationnels dépasse les limites.
Dans la plupart des cas, cela est causé par une requête qui contient une longue liste d’opérateurs binaires enchaînés, par exemple:

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

Pour ce cas spécifique - il est recommandé [`in()`](../query/inoperator.md) de ré-écrire la requête à l’aide de l’opérateur. 

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```