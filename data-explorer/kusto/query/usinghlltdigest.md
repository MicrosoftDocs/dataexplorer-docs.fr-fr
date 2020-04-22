---
title: Partitionnement et composition de résultats intermédiaires d’agrégations - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le partitionnement et la composition de résultats intermédiaires d’agrégations dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1392105e4b5d99f2273b686ed74a161750ee6f8c
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81662961"
---
# <a name="partitioning-and-composing-intermediate-results-of-aggregations"></a>Partitionnement et composition des résultats intermédiaires des agrégations

Supposons que vous voulez calculer le nombre d’utilisateurs distincts au cours des sept derniers jours, tous les jours. Une façon de le faire `summarize dcount(user)` serait d’exécuter une fois par jour avec une portée filtrée aux sept derniers jours. Ceci est inefficace parce que chaque fois que le calcul est exécuté, il y a un chevauchement de six jours avec le calcul précédent. Une autre option consiste à calculer un certain agrégat pour chaque jour, puis à combiner ces agrégats de manière efficace. Cette option vous oblige à « se souvenir » des six derniers résultats, mais elle est beaucoup plus efficace.

Partitionner des requêtes comme celle-là serait facile pour les agrégats simples, tels que le compte () et la somme (). Il est toutefois possible de les exécuter également pour des agrégats plus complexes, tels que les dcount() et les percentiles (). Ce sujet explique comment Kusto prend en charge de tels calculs.

Les exemples suivants montrent comment utiliser hll/tdigest et démontre que l’utilisation de ces scénarios peut être beaucoup plus performante que d’autres façons:

> [!NOTE]
> Dans certains cas, les objets `hll` dynamiques générés par les fonctions ou les `tdigest` fonctions agrégées peuvent être grands et dépasser la propriété MaxValueSize par défaut dans la politique d’encodage. Si c’est le cas, l’objet sera ingéré comme nul.
Par exemple, lorsque vous `hll` persistez la sortie de fonction avec le niveau de précision 4, la taille de l’objet hll dépasse la valeur maximale par défaut qui est de 1 Mo.

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1.0000524520874|

L’ingestion de cette politique dans un tableau avant d’appliquer ce genre de politique sera nulle :

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

```kusto
MyTable
| project isempty(hll_x)
```

| Colonne1 |
|---------|
| 1       |

Pour éviter cela, utilisez le `bigobject`type de politique d’encodage spécial , qui remplace le MaxValueSize à 2 Mo comme ceci:

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```



Donc, l’ingestion d’une valeur maintenant à la même table ci-dessus:

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```
ingérer la deuxième valeur avec succès : 

```kusto
MyTable
| project isempty(hll_x)
```

|Colonne1|
|---|
|1|
|0|


**Exemple**

Supposons qu’il y ait un tableau, PageViewsHllTDigest, qui a les valeurs de hll sur les pages vues dans chaque heure. L’objectif est d’obtenir ces `12h`valeurs, mais binned à , de fusionner les valeurs hll en `12h`utilisant la fonction d’agrégat hll_merge () par timestamp binned à . Ensuite, appelez la fonction dcount_hll pour obtenir la valeur finale dcount:

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|dcount_hll_merged_hll|
|---|---|
|2016-05-01 12:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|38797623|
|2016-05-02 12:00:00.0000000|39316056|
|2016-05-03 00:00:00.0000000|13685621|

Ou même pour l’amampe de temps binned pour `1d`:

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|dcount_hll_merged_hll|
|---|---|
|2016-05-01 00:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|64135183|
|2016-05-03 00:00:00.0000000|13685621|

 La même chose peut être faite sur les valeurs de tdigest, qui représentent les BytesDelivered dans chaque heure:

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|Timestamp|percentile_tdigest_merged_tdigests|
|---|---|
|2016-05-01 12:00:00.0000000|170200|
|2016-05-02 00:00:00.0000000|152975|
|2016-05-02 12:00:00.0000000|181315|
|2016-05-03 00:00:00.0000000|146817|
 
**Exemple**

Les limites de Kusto sont atteintes avec des jeux de données trop grands, où vous devez [`percentile()`](percentiles-aggfunction.md) exécuter [`dcount()`](dcount-aggfunction.md) des requêtes périodiques sur le jeu de données, mais exécutez les requêtes régulières pour calculer ou sur ces ensembles de données Big.

::: zone pivot="azuredataexplorer"

Pour résoudre ce problème, des données nouvellement ajoutées peuvent être ajoutées à une table d’température sous forme de valeurs de hll ou de tdigestes utilisant [`hll()`](hll-aggfunction.md) lorsque l’opération requise est d’arrêt ou [`tdigest()`](tdigest-aggfunction.md) lorsque l’opération requise est percentile en utilisant [`set/append`](../management/data-ingestion/index.md) ou, [`update policy`](../management/updatepolicy.md)Dans ce cas, les résultats intermédiaires de dcount ou tdigest sont enregistrés dans un autre jeu de données, qui devrait être plus petit que le grand cible.

::: zone-end

::: zone pivot="azuremonitor"

Pour résoudre ce problème, des données nouvellement ajoutées peuvent être ajoutées à un tableau d’intérim sous forme de valeurs de hll ou de tdigestes utilisant [`hll()`](hll-aggfunction.md) lorsque l’opération requise est dcount, Dans ce cas, les résultats intermédiaires de dcount sont enregistrés dans un autre jeu de données, qui devrait être plus petit que le grand cible.

::: zone-end

Lorsque vous avez besoin d’obtenir les résultats finaux de ces valeurs, [`hll-merge()`](hll-merge-aggfunction.md) / [`tdigest_merge()`](tdigest-merge-aggfunction.md)les requêtes peuvent utiliser des [`percentile_tdigest()`](percentile-tdigestfunction.md)  /  [`dcount_hll()`](dcount-hllfunction.md) fusions hll/ tdigest: , Puis, après avoir obtenu les valeurs fusionnées, peut être invoqué sur ces valeurs fusionnées pour obtenir le résultat final de dcount ou percentiles.

En supposant qu’il y ait un tableau, PageViews, dans lequel les données sont ingérées quotidiennement, tous les jours sur lesquels vous voulez calculer le nombre distinct de pages consultées par minuite plus tard que la date et la date(2016-05-01 18:00:00.0000000).

Exécutez la requête suivante :

```kusto
PageViews   
| where Timestamp > datetime(2016-05-01 18:00:00.0000000)
| summarize percentile(BytesDelivered, 90), dcount(Page,2) by bin(Timestamp, 1d)
```

|Timestamp|percentile_BytesDelivered_90|dcount_Page|
|---|---|---|
|2016-05-01 00:00:00.0000000|83634|20056275|
|2016-05-02 00:00:00.0000000|82770|64135183|
|2016-05-03 00:00:00.0000000|72920|13685621|

Cette requête regroupera toutes les valeurs chaque fois que vous exécutez cette requête (par exemple, si vous voulez l’exécuter plusieurs fois par jour).

Si vous enregistrez les valeurs de hll et de tdigest (qui sont les résultats intermédiaires du dcount et du percentile) dans une table d’intérim, PageViewsHllTDigest, en utilisant une stratégie de mise à jour ou des commandes de set/appendice, vous ne pouvez fusionner les valeurs et ensuite utiliser dcount_hll/percentile_tdigest en utilisant la requête suivante :

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|Timestamp|percentile_tdigest_merge_tdigests_tdigestBytesDel|dcount_hll_hll_merge_hllPage|
|---|---|---|
|2016-05-01 00:00:00.0000000|84224|20056275|
|2016-05-02 00:00:00.0000000|83486|64135183|
|2016-05-03 00:00:00.0000000|72247|13685621|

Cela devrait être plus performant et la requête s’étend sur une table plus petite (dans cet exemple, la première requête s’étend sur 215 millions d’enregistrements tandis que le second fonctionne sur 32 enregistrements seulement).

**Exemple**

La requête de rétention.
En supposant que vous ayez un tableau qui résume quand chaque page Wikipédia a été consultée (la taille de l’échantillon est de 10M), et que vous souhaitez trouver pour chaque date1 2 le pourcentage de pages examinées à la fois dans la date1 et la date2 par rapport aux pages vues à la date1 (date1 < date2).
  
La façon triviale utilise joindre et résumer les opérateurs :

```kusto
// Get the total pages viewed each day
let totalPagesPerDay = PageViewsSample
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
// Join the table to itself to get a grid where 
// each row shows foreach page1, in which two dates
// it was viewed.
// Then count the pages between each two dates to
// get how many pages were viewed between date1 and date2.
PageViewsSample
| summarize by Page, Day1 = startofday(Timestamp)
| join kind = inner
(
    PageViewsSample
    | summarize by Page, Day2 = startofday(Timestamp)
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

|Jour1|Jour2|Pourcentage|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|

 
La requête ci-dessus a pris 18 secondes.

Lors de l’utilisation [`hll_merge()`](hll-merge-aggfunction.md) [`dcount_hll()`](dcount-hllfunction.md)des fonctions de [`hll()`](hll-aggfunction.md), et , la requête équivalente se terminera après 1,3 secondes et montre que les fonctions hll accélère la requête ci-dessus par 14 fois:

```kusto
let Stats=PageViewsSample | summarize pagehll=hll(Page, 2) by day=startofday(Timestamp); // saving the hll values (intermediate results of the dcount values)
let day0=toscalar(Stats | summarize min(day)); // finding the min date over all dates.
let dayn=toscalar(Stats | summarize max(day)); // finding the max date over all dates.
let daycount=tolong((dayn-day0)/1d); // finding the range between max and min
Stats
| project idx=tolong((day-day0)/1d), day, pagehll
| mv-expand pidx=range(0, daycount) to typeof(long)
// Extend the column to get the dcount value from hll'ed values for each date (same as totalPagesPerDay from the above query)
| extend key1=iff(idx < pidx, idx, pidx), key2=iff(idx < pidx, pidx, idx), pages=dcount_hll(pagehll)
// For each two dates, merge the hll'ed values to get the total dcount over each two dates, 
// This helps to get the pages viewed in both date1 and date2 (see the description below about the intersection_size)
| summarize (day1, pages1)=arg_min(day, pages), (day2, pages2)=arg_max(day, pages), union_size=dcount_hll(hll_merge(pagehll)) by key1, key2
| where day2 > day1
// To get pages viewed in date1 and also date2, look at the merged dcount of date1 and date2, subtract it from pages of date1 + pages on date2.
| project pages1, day1,day2, intersection_size=(pages1 + pages2 - union_size)
| project day1, day2, Percentage = intersection_size*100.0 / pages1
```

|jour1|jour2|Pourcentage|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|33.2298494510578|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.9773830213667|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.5160020350006|

> [!NOTE] 
> Les résultats des requêtes ne sont pas précis à 100% en raison de l’erreur des fonctions de hll. (voir [`dcount()`](dcount-aggfunction.md) pour plus d’informations sur les erreurs).
