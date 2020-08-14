---
title: Requête de lecture aléatoire-Azure Explorateur de données
description: Cet article décrit la lecture aléatoire des requêtes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d3625be5a3a97b456a2d6d84802b11602f959f3e
ms.sourcegitcommit: bb7c2ba9f9dcae08710be2345ee6e63004629ea1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218977"
---
# <a name="shuffle-query"></a>Requête de lecture aléatoire

La lecture aléatoire de requête est une transformation de préservation sémantique pour un ensemble d’opérateurs qui prennent en charge la stratégie de lecture aléatoire. Selon les données réelles, cette requête peut obtenir de meilleures performances.

Les opérateurs qui prennent en charge la [réassociation](joinoperator.md)dans Kusto sont Join, [Resume](summarizeoperator.md)et [Make-Series](make-seriesoperator.md).

Définissez une stratégie de requête de lecture aléatoire à l’aide du paramètre de requête `hint.strategy = shuffle` ou `hint.shufflekey = <key>` .

## <a name="syntax"></a>Syntaxe

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

Cette stratégie va partager la charge sur tous les nœuds de cluster, où chaque nœud traitera une partition de données.
Il est utile d’utiliser la stratégie de requête de lecture aléatoire lorsque la clé ( `join` clé, `summarize` clé ou `make-series` clé) a une cardinalité élevée et que la stratégie de requête régulière atteint les limites de requête.

**Différence entre hint. Strategy = lecture aléatoire et hint. shufflekey = clé**

`hint.strategy=shuffle` signifie que l’opérateur aléatoire est mélangé par toutes les clés.
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

Si la clé composée est trop unique, mais que chaque clé n’est pas suffisamment unique, utilisez- `hint` la pour mélanger les données par toutes les clés de l’opérateur aléatoire.
Lorsque l’opérateur de lecture aléatoire a d’autres opérateurs en lecture seule, comme `summarize` ou `join` , la requête devient plus complexe, puis hint. Strategy = la lecture aléatoire n’est pas appliquée.

Par exemple :

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

Si vous appliquez `hint.strategy=shuffle` (au lieu d’ignorer la stratégie lors de la planification des requêtes) et que vous mélangez les données par la clé composée [ `ActivityId` , `numeric_column` ], le résultat n’est pas correct.
L' `summarize` opérateur se trouve sur le côté gauche de l' `join` opérateur. Cet opérateur regroupera par un sous-ensemble des `join` clés, qui, dans notre cas, est `ActivityId` . Par conséquent, le `summarize` regroupera par clé `ActivityId` , tandis que les données seront partitionnées par la clé composée [ `ActivityId` , `numeric_column` ].
La combinaison de la clé composée [ `ActivityId` , `numeric_column` ] ne signifie pas nécessairement que la combinaison de la clé `ActivityId` est valide et que les résultats peuvent être incorrects.

Cet exemple suppose que la fonction de hachage utilisée pour une clé composée est `binary_xor(hash(key1, 100) , hash(key2, 100))` :

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

La clé composée pour les deux enregistrements a été mappée à des partitions différentes (56 et 65), mais ces deux enregistrements ont la même valeur `ActivityId` . L' `summarize` opérateur situé à gauche du `join` s’attend à ce que les valeurs similaires de la colonne `ActivityId` se trouvent dans la même partition. Cette requête génère des résultats incorrects.

Vous pouvez résoudre ce problème à l’aide `hint.shufflekey` de pour spécifier la clé de lecture aléatoire sur la jointure `hint.shufflekey = ActivityId` . Cette clé est courante pour tous les opérateurs pouvant être en lecture seule.
Dans ce cas, la permutation est sûre, car `join` et `summarize` par la même clé. Par conséquent, toutes les valeurs similaires se trouvent dans la même partition et les résultats sont corrects :

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

Dans une requête de lecture aléatoire, le numéro de partition par défaut est le numéro des nœuds du cluster. Ce nombre peut être remplacé à l’aide de la syntaxe `hint.num_partitions = total_partitions` , qui contrôle le nombre de partitions.

Cet indicateur est utile lorsque le cluster a un petit nombre de nœuds de cluster dans lesquels le numéro de partition par défaut est trop petit et que la requête échoue toujours ou prend beaucoup de temps.

> [!Note]
> Le fait de disposer de nombreuses partitions peut consommer davantage de ressources de cluster et dégrader les performances. Au lieu de cela, choisissez le numéro de partition avec précaution en commençant par l’indicateur. Strategy = aléatoire et commencez à améliorer progressivement les partitions.

## <a name="examples"></a>Exemples

L’exemple suivant montre comment la lecture aléatoire `summarize` améliore considérablement les performances.

La table source comporte 150 000 enregistrements et la cardinalité de la clé Group by est de 10 m, qui est répartie sur 10 nœuds de cluster.

En exécutant la `summarize` stratégie régulière, la requête se termine après 1:08 et le pic d’utilisation de la mémoire est ~ 3 Go :

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Nombre|
|---|
|1086|

Lors de l’utilisation de la stratégie de lecture aléatoire `summarize` , la requête se termine après environ 7 secondes et le pic d’utilisation de la mémoire est de 0,43 Go :

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Nombre|
|---|
|1086|

L’exemple suivant illustre l’amélioration apportée à un cluster qui a deux nœuds de cluster, la table contient des enregistrements 60 min et la cardinalité de la clé Group by est 2M.

L’exécution de la requête sans `hint.num_partitions` utilisera uniquement deux partitions (comme nombre de nœuds de cluster) et la requête suivante prendra environ 1:10 minutes :

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

L’exemple suivant montre comment la lecture aléatoire `join` améliore considérablement les performances.

Les exemples ont été échantillonnés sur un cluster comportant 10 nœuds dans lesquels les données sont réparties sur l’ensemble de ces nœuds.

La table de gauche contient des enregistrements 15 millions où la cardinalité de la `join` clé est ~ 14M. Le côté droit du `join` est avec des enregistrements de 150 m et la cardinalité de la `join` clé est de 10 m.
En exécutant la stratégie normale du `join` , la requête se termine après environ 28 secondes et le pic d’utilisation de la mémoire est de 1,43 Go :

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Lors de l’utilisation de la stratégie de lecture aléatoire `join` , la requête se termine après environ 4 secondes et le pic d’utilisation de la mémoire est de 0,3 Go :

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

En essayant les mêmes requêtes sur un plus grand jeu de données où la partie gauche de la `join` est 150 m et la cardinalité de la clé est 148M. Le côté droit du `join` est 1,5 b et la cardinalité de la clé est ~ 100 m.

La requête avec la stratégie par défaut `join` atteint les limites et les délais Kusto après 4 minutes.
Lors de l’utilisation de `join` la stratégie de lecture aléatoire, la requête se termine après environ 34 secondes et le pic d’utilisation de la mémoire est de 1,23 Go.


L’exemple suivant illustre l’amélioration apportée à un cluster qui a deux nœuds de cluster, la table contient des enregistrements 60 min et la cardinalité de la `join` clé est de 2 m.
L’exécution de la requête sans `hint.num_partitions` utilisera uniquement deux partitions (comme nombre de nœuds de cluster) et la requête suivante prendra environ 1:10 minutes :

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
