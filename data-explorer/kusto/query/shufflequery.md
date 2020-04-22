---
title: Questions Shuffle - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la requête Shuffle dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c687d495a41a5f73ac8dbca15d93729f2132a556
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81662972"
---
# <a name="shuffle-query"></a>Questions shuffle

La requête Shuffle est une transformation sémantique pour un ensemble d’opérateurs qui prend en charge la stratégie de shuffle qui, selon les données réelles, peut produire des performances considérablement meilleures.

Opérateurs qui prend en charge le brassage à Kusto sont [rejoindre](joinoperator.md), [résumer](summarizeoperator.md) et [faire-série](make-seriesoperator.md).

La stratégie de requête shuffle peut `hint.strategy = shuffle` être `hint.shufflekey = <key>`définie par le paramètre de requête ou .

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

Cette stratégie partagera la charge sur tous les nœuds de cluster où chaque nœud traitera une partition des données.
Il est utile d’utiliser la stratégie`join` de `summarize` requête `make-series` shuffle lorsque la clé (clé, clé ou clé) a une cardinalité élevée provoquant la stratégie de requête régulière pour atteindre les limites de requête.

**Différence entre hint.strategy-shuffle et hint.shufflekey - clé**

`hint.strategy=shuffle`signifie que l’opérateur mélangé sera mélangé par toutes les clés.
Par exemple, dans cette requête :

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

La fonction de hachage qui mélange les données utilisera à la fois les touches ActivityId et ProcessId.

La requête ci-dessus est équivalente à :

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Cet indice peut être utilisé lorsque vous êtes intéressé à mélanger les données par toutes les clés de l’opérateur mélangé parce que la clé composée est trop unique, mais chaque clé n’est pas assez unique.
Lorsque l’opérateur mélangé a d’autres `summarize` `join`opérateurs shufflable comme ou , la requête devient plus complexe, puis hint.strategy-shuffle ne sera pas appliquée.

par exemple :

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

Dans ce cas, si `hint.strategy=shuffle` nous appliquons le (au lieu d’ignorer la stratégie pendant`ActivityId` `numeric_column`la planification de requête) et mélanger les données par la clé composée [, ] le résultat ne sera pas correct.
Le `summarize` qui est sur le `join` côté gauche des groubs par un sous-ensemble des `join` touches qui est `ActivityId`. Cela signifie `summarize` que le groupe `ActivityId` de volonté par la clé`ActivityId`tandis `numeric_column`que les données sont partitionnées par la clé composée [, ].
Shuffling par la`ActivityId`clé `numeric_column`composée [ , ] ne signifie pas qu’il s’agit d’un mélange valide pour l’activité cléId et les résultats peuvent être incorrects.

Cet exemple simplifie cette hypothèse en supposant que la fonction de hachage utilisée pour une clé composée est`binary_xor(hash(key1, 100) , hash(key2, 100))`

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
|activité1|2|56|
|activité1|1|65|



Comme vous le voyez, la clé composée pour les deux enregistrements a été cartographiée à `ActivityId` différentes partitions 56 et 65, mais ces deux enregistrements ont la même valeur de ce qui signifie que le `summarize` côté gauche de la `join` qui s’attend à des valeurs similaires de la colonne `ActivityId` d’être dans la même partition produira définitivement de mauvais résultats.

Dans ce `hint.shufflekey` cas, résout ce problème en spécifiant la clé de mélange sur la jointure à `hint.shufflekey = ActivityId` laquelle est une clé commune pour tous les opérateurs shuffelables.
Dans ce cas, le brassage `join` est `summarize` sûr, à la fois et shuffles par la même clé de sorte que toutes les valeurs similaires seront définitivement dans la même partition les résultats sont corrects:

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
|activité1|2|56|
|activité1|1|65|

Dans la requête shuffle, le numéro de partitions par défaut est le numéro de nœuds de cluster. Ce nombre peut être remplacé par `hint.num_partitions = total_partitions` l’utilisation de la syntaxe qui contrôlera le nombre de cloisons.

Cet indice est utile lorsque le cluster a un petit nombre de nœuds cluster où le nombre de cloisons par défaut sera faible aussi et la requête échoue encore ou prend beaucoup de temps d’exécution.

Notez que le réglage de nombreuses partitions peut dégrader les performances et consommer plus de ressources en grappes de sorte qu’il est recommandé de choisir le numéro de partition avec soin (en commençant par le hint.strategy - mélanger et commencer à augmenter les partitions progressivement).

**Exemples**

L’exemple suivant `summarize` montre comment le mélange améliore considérablement les performances.

Le tableau source a 150M enregistrements et la cardinalité du groupe par clé est de 10M qui est répartie sur 10 nœuds de cluster.

Exécution de `summarize` la stratégie régulière, la requête se termine après 1:08 et le pic d’utilisation de la mémoire est de 3 Go:

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

Tout en `summarize` utilisant la stratégie de mélange, la requête se termine après 7 secondes et le pic d’utilisation de la mémoire est de 0,43 Go:

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

L’exemple suivant montre l’amélioration d’un cluster qui a 2 nœuds de cluster, la table a des enregistrements de 60M et la cardinalité du groupe par clé est 2M.

Exécution de la `hint.num_partitions` requête sans utilisera seulement 2 partitions (comme numéro de nœuds cluster) et la requête suivante prendra 1:10 minutes:

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

en fixant le numéro des partitions à 10, la requête se terminera après 23 secondes : 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

L’exemple suivant `join` montre comment le mélange améliore considérablement les performances.

Les exemples ont été échantillonnés sur un cluster avec 10 nœuds où les données sont réparties sur tous ces nœuds.

La table de gauche a 15M `join` enregistrements où la cardinalité de la `join` clé est de 14M, Le `join` côté droit de l’est avec des dossiers de 150M et la cardinalité de la clé est de 10M.
Exécution de la `join`stratégie régulière de la , la requête se termine après 28 secondes et le pic d’utilisation de la mémoire est de 1,43 Go:

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Tout en `join` utilisant la stratégie de mélange, la requête se termine après 4 secondes et le pic d’utilisation de la mémoire est de 0,3 Go :

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Essayer les mêmes requêtes sur un ensemble `join` de données plus large où le côté gauche de la est de `join` 150M et la cardinalité de la clé est de 148M, côté droit de l’est de 1,5B et la cardinalité de la clé est de 100M.

La requête avec `join` la stratégie par défaut atteint les limites kusto et les temps d’out après 4 minutes.
Tout en `join` utilisant la stratégie de mélange, la requête se termine après 34 secondes et le pic d’utilisation de la mémoire est de 1,23 Go.


L’exemple suivant montre l’amélioration d’un cluster qui a 2 nœuds de `join` cluster, la table a des enregistrements de 60M et la cardinalité de la clé est 2M.
Exécution de la `hint.num_partitions` requête sans utilisera seulement 2 partitions (comme numéro de nœuds cluster) et la requête suivante prendra 1:10 minutes:

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

en fixant le numéro des partitions à 10, la requête se terminera après 23 secondes : 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```