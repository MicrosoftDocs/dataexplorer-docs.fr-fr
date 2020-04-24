---
title: Requête de lecture aléatoire-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la lecture aléatoire des requêtes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 600e561937b779ff9dd10d5d82f5522d204466a0
ms.sourcegitcommit: 2e63c7c668c8a6200f99f18e39c3677fcba01453
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117690"
---
# <a name="shuffle-query"></a>Lecture aléatoire des requêtes

La lecture aléatoire d’une requête est une transformation de préservation sémantique pour un ensemble d’opérateurs qui prend en charge la stratégie de lecture aléatoire, selon laquelle les données réelles peuvent produire de meilleures performances.

Les opérateurs qui prennent en charge la [réassociation](joinoperator.md)dans Kusto sont Join, [Resume](summarizeoperator.md) et [Make-Series](make-seriesoperator.md).

La stratégie de requête de lecture aléatoire peut être définie `hint.strategy = shuffle` par `hint.shufflekey = <key>`le paramètre de requête ou.

Vous pouvez également examiner la définition d’une [stratégie de partitionnement de données](../management/partitioningpolicy.md) sur votre table. Les requêtes dans lesquelles `shufflekey` est également la clé de partition de hachage de la table sont supposées être plus performantes, car la quantité de données requises pour le déplacement entre les nœuds de cluster est considérablement réduite.

**Syntaxe**

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

```kusto
T
| summarize hint.strategy = shuffle count(), avg(price) by supplier
```

```kusto
T
| make-series hint.shufflekey = Fruit PriceAvg=avg(Price) default=0  on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

Cette stratégie partagera la charge sur tous les nœuds de cluster, où chaque nœud traitera une partition de données.
Il est utile d’utiliser la stratégie de requête de lecture aléatoire lorsque`join` la clé `summarize` (clé `make-series` , clé ou clé) a une cardinalité élevée qui amène la stratégie de requête normale à atteindre les limites de requête.

**Différence entre hint. Strategy = lecture aléatoire et hint. shufflekey = clé**

`hint.strategy=shuffle`signifie que l’opérateur aléatoire est mélangé par toutes les clés.
Par exemple, dans cette requête :

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

La fonction de hachage qui effectue une lecture aléatoire des données utilise à la fois les clés ActivityId et ProcessId.

La requête ci-dessus équivaut à :

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Cet indicateur peut être utilisé lorsque vous êtes intéressé par la permutation des données par toutes les clés de l’opérateur aléatoire, car la clé composée est trop unique, mais chaque clé n’est pas suffisamment unique.
Lorsque l’opérateur aléatoire a d’autres opérateurs shufflable comme `summarize` ou `join`, la requête devient plus complexe, puis hint. Strategy = la lecture aléatoire n’est pas appliquée.

par exemple :

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.strategy = shuffle (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Dans ce cas, si nous appliquons `hint.strategy=shuffle` (au lieu d’ignorer la stratégie lors de la planification des requêtes) et que vous mélangez de façon aléatoire`ActivityId`les `numeric_column`données par la clé composée [,] le résultat sera incorrect.
`summarize` Qui se trouve sur le côté gauche du `join` groubs par un sous-ensemble des `join` clés qui est `ActivityId`. Cela signifie que le `summarize` regroupera par la `ActivityId` clé alors que les données seront partitionnées par la clé`ActivityId`composée `numeric_column`[,].
La combinaison de la clé composée`ActivityId`[ `numeric_column`,] ne signifie pas qu’il s’agit d’une combinaison valide pour la clé ActivityId et que les résultats peuvent être incorrects.

Cet exemple simplifie cela en supposant que la fonction de hachage utilisée pour une clé composée est`binary_xor(hash(key1, 100) , hash(key2, 100))`

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|NumericColumn|hash_by_key|
|---|---|---|
|Activity1|2|56|
|Activity1|1|65|



Comme vous pouvez le voir, la clé composée des deux enregistrements a été mappée à des partitions différentes 56 et 65, mais ces deux enregistrements `ActivityId` ont la même valeur `summarize` , ce qui signifie que le `join` côté gauche du qui attend des valeurs similaires `ActivityId` de la colonne dans la même partition defintely produire des résultats incorrects.

Dans ce cas, `hint.shufflekey` résout ce problème en spécifiant la clé de permutation sur `hint.shufflekey = ActivityId` la jointure qui est une clé commune pour tous les opérateurs shuffelable.
Dans ce cas, la réarrangement est sécurisée `join` , `summarize` et est aléatoire par la même clé, de sorte que toutes les valeurs similaires seront defintely dans la même partition. les résultats sont corrects :

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.shufflekey = ActivityId (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

|ActivityId|NumericColumn|hash_by_key|
|---|---|---|
|Activity1|2|56|
|Activity1|1|65|

Dans une requête de lecture aléatoire, le numéro de partition par défaut est le numéro des nœuds du cluster. Ce nombre peut être remplacé à l’aide de la syntaxe `hint.num_partitions = total_partitions` qui contrôle le nombre de partitions.

Cet indicateur est utile lorsque le cluster a un petit nombre de nœuds de cluster dans lesquels le numéro de partition par défaut est trop petit et que la requête échoue toujours ou prend beaucoup de temps.

Notez que le fait de définir de nombreuses partitions peut dégrader les performances et consommer davantage de ressources de cluster. il est donc recommandé de choisir le numéro de partition avec précaution (en commençant par l’indicateur. Strategy = en lecture seule et commencer à augmenter graduellement les partitions).

**Exemples**

L’exemple suivant montre comment la `summarize` lecture aléatoire améliore considérablement les performances.

La table source comporte 150 000 enregistrements et la cardinalité de la clé Group by est de 10 m qui est répartie sur 10 nœuds de cluster.

En exécutant la `summarize` stratégie régulière, la requête se termine après 1:08 et le pic d’utilisation de la mémoire est d’environ 3 Go :

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

Lors de l' `summarize` utilisation de la stratégie de lecture aléatoire, la requête se termine après environ 7 secondes et le pic d’utilisation de la mémoire est de 0.43 Go :

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

L’exemple suivant illustre l’amélioration apportée à un cluster qui a 2 nœuds de cluster, la table contient des enregistrements 60 min et la cardinalité de la clé Group by est 2M.

L’exécution de la `hint.num_partitions` requête sans utilisera uniquement 2 partitions (comme nombre de nœuds de cluster) et la requête suivante prendra environ 1:10 minutes :

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

Si vous définissez le nombre de partitions sur 10, la requête se termine après 23 secondes : 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

L’exemple suivant montre comment la `join` lecture aléatoire améliore considérablement les performances.

Les exemples ont été échantillonnés sur un cluster comportant 10 nœuds dans lesquels les données sont réparties sur l’ensemble de ces nœuds.

La table de gauche contient des enregistrements 15 millions où la cardinalité `join` de la clé est ~ 14M, le côté droit `join` du est avec des enregistrements de 150 m et la `join` cardinalité de la clé est de 10 m.
En exécutant la stratégie normale du `join`, la requête se termine après environ 28 secondes et le pic d’utilisation de la mémoire est de 1.43 Go :

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Lors de l' `join` utilisation de la stratégie de lecture aléatoire, la requête se termine après environ 4 secondes et le pic d’utilisation de la mémoire est de 0,3 Go :

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

En essayant les mêmes requêtes sur un jeu de données plus grand où `join` la partie gauche de la est 150 m et la cardinalité de la clé est `join` 148M, la partie droite du est 1,5 b et la cardinalité de la clé est ~ 100 $.

La requête avec la stratégie `join` par défaut atteint les limites et les délais Kusto après 4 minutes.
Lors de l' `join` utilisation de la stratégie de lecture aléatoire, la requête se termine après environ 34 secondes et le pic d’utilisation de la mémoire est de 1,23 Go.


L’exemple suivant illustre l’amélioration apportée à un cluster qui a 2 nœuds de cluster, la table contient des enregistrements 60 min `join` et la cardinalité de la clé est de 2m.
L’exécution de la `hint.num_partitions` requête sans utilisera uniquement 2 partitions (comme nombre de nœuds de cluster) et la requête suivante prendra environ 1:10 minutes :

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

Si vous définissez le nombre de partitions sur 10, la requête se termine après 23 secondes : 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```
