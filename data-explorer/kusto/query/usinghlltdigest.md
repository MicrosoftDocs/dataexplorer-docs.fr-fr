---
title: Kusto partition & compose les résultats de l’agrégation intermédiaire-Azure Explorateur de données
description: Cet article décrit le partitionnement et la composition des résultats intermédiaires des agrégations dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 30d4f6bd315b5a32c67570ab16b9abc3160f0177
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954482"
---
# <a name="using-hll-and-tdigest"></a>Utilisation de hll() et de tdigest()

Supposons que vous souhaitez calculer le nombre d’utilisateurs distincts tous les jours au cours des sept derniers jours. Vous pouvez exécuter une `summarize dcount(user)` fois par jour avec une étendue filtrée sur les sept derniers jours. Cette méthode est inefficace, car chaque fois que le calcul est exécuté, il existe un chevauchement de six jours avec le calcul précédent. Vous pouvez également calculer un agrégat pour chaque jour, puis combiner ces agrégats. Cette méthode vous oblige à « mémoriser » les six derniers résultats, mais c’est bien plus efficace.

Le partitionnement des requêtes comme décrit est facile pour les agrégats simples, tels que `count()` et `sum()` . Il peut également être utile pour les agrégats complexes, tels que `dcount()` et `percentiles()` . Cette rubrique explique comment Kusto prend en charge ces calculs.

Les exemples suivants montrent comment utiliser `hll` / `tdigest` et montrent que l’utilisation de ces commandes est très performante dans certains scénarios :

> [!NOTE]
> Dans certains cas, les objets dynamiques générés par `hll` ou les `tdigest` fonctions d’agrégation peuvent être volumineux et dépasser la valeur par défaut de la propriété MaxValueSize dans la stratégie d’encodage. Dans ce cas, l’objet est ingéré comme null.
> Par exemple, lors de la persistance de la sortie de la `hll` fonction avec un niveau de précision 4, la taille de l' `hll` objet dépasse la valeur par défaut de MaxValueSize, soit 1 Mo.
> Pour éviter ce problème, modifiez la stratégie d’encodage de la colonne comme indiqué dans les exemples suivants.

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1,0000524520874|

L’ingestion de cet objet dans une table avant d’appliquer ce type de stratégie ingérera la valeur NULL :

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

```kusto
MyTable
| project isempty(hll_x)
```

| Column1 |
|---------|
| 1       |

Pour éviter d’ingérer la valeur null, utilisez le type de stratégie d’encodage spécial `bigobject` , qui remplace le `MaxValueSize` par 2 Mo comme suit :

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```

Réception d’une valeur maintenant dans le même tableau ci-dessus :

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

ingère la deuxième valeur avec succès : 

```kusto
MyTable
| project isempty(hll_x)
```

|Column1|
|---|
|1|
|0|


## <a name="example-count-with-binned-timestamp"></a>Exemple : Count avec horodateur Binned (

Il existe une table, `PageViewsHllTDigest` , contenant `hll` les valeurs des pages affichées dans chaque heure. Vous souhaitez que ces valeurs Binned (ent à `12h` . Fusionnez les `hll` valeurs à l’aide de la `hll_merge()` fonction d’agrégation, avec l’horodateur Binned (à `12h` . Utilisez la fonction `dcount_hll` pour retourner la `dcount` valeur finale :

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 12:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|38797623|
|2016-05-02 12:00:00.0000000|39316056|
|2016-05-03 00:00:00.0000000|13685621|

Horodatage de casier pour `1d` :

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 00:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|64135183|
|2016-05-03 00:00:00.0000000|13685621|

La même requête peut être effectuée sur les valeurs de `tdigest` , qui représentent le `BytesDelivered` en toutes les heures :

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|Timestamp|`percentile_tdigest_merged_tdigests`|
|---|---|
|2016-05-01 12:00:00.0000000|170200|
|2016-05-02 00:00:00.0000000|152975|
|2016-05-02 12:00:00.0000000|181315|
|2016-05-03 00:00:00.0000000|146817|
 
## <a name="example-temporary-table"></a>Exemple : table temporaire

Les limites de Kusto sont atteintes avec les jeux de données trop volumineux, où vous devez exécuter des requêtes périodiques sur le jeu de données, mais exécuter les requêtes régulières pour calculer [`percentile()`](percentiles-aggfunction.md) ou [`dcount()`](dcount-aggfunction.md) sur des jeux de données volumineux.

::: zone pivot="azuredataexplorer"

Pour résoudre ce problème, des données nouvellement ajoutées peuvent être ajoutées à une table temporaire en tant que `hll` `tdigest` valeurs ou à l’aide de [`hll()`](hll-aggfunction.md) lorsque l’opération requise est `dcount` ou [`tdigest()`](tdigest-aggfunction.md) lorsque l’opération requise est un centile à l’aide [`set/append`](../../ingest-data-overview.md) de ou de [`update policy`](../management/updatepolicy.md) . Dans ce cas, les résultats intermédiaires de `dcount` ou `tdigest` sont enregistrés dans un autre jeu de données, qui doit être plus petit que le plus grand cible.

::: zone-end

::: zone pivot="azuremonitor"

Pour résoudre ce problème, il est possible d’ajouter des données nouvellement ajoutées à une table temporaire en tant que `hll` `tdigest` valeurs ou à l’aide de [`hll()`](hll-aggfunction.md) lorsque l’opération requise est `dcount` . Dans ce cas, les résultats intermédiaires de `dcount` sont enregistrés dans un autre jeu de données, qui doit être plus petit que le plus grand cible.

::: zone-end

Lorsque vous avez besoin d’obtenir les résultats finaux de ces valeurs, les requêtes peuvent utiliser des `hll` / `tdigest` fusions : [`hll-merge()`](hll-merge-aggfunction.md) / [`tdigest_merge()`](tdigest-merge-aggfunction.md) . Ensuite, après avoir obtenu les valeurs fusionnées, [`percentile_tdigest()`](percentile-tdigestfunction.md)  /  [`dcount_hll()`](dcount-hllfunction.md) peut être appelé sur ces valeurs fusionnées pour obtenir le résultat final de `dcount` ou des centile.

En supposant qu’il existe une table, PageViews, dans laquelle les données sont ingérées quotidiennement, chaque jour sur lequel vous souhaitez calculer le nombre de pages affichées par minute plus tard que date = DateTime (2016-05-01 18:00:00.0000000).

Exécutez la requête suivante :

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

Cette requête regroupe toutes les valeurs chaque fois que vous exécutez cette requête (par exemple, si vous souhaitez l’exécuter plusieurs fois par jour).

Si vous enregistrez les `hll` `tdigest` valeurs et (qui sont les résultats intermédiaires de `dcount` et centile) dans une table temporaire, `PageViewsHllTDigest` , à l’aide d’une stratégie de mise à jour ou de commandes Set/Append, vous pouvez fusionner uniquement les valeurs, puis utiliser `dcount_hll` / `percentile_tdigest` la requête suivante :

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|Timestamp|`percentile_tdigest_merge_tdigests_tdigestBytesDel`|`dcount_hll_hll_merge_hllPage`|
|---|---|---|
|2016-05-01 00:00:00.0000000|84224|20056275|
|2016-05-02 00:00:00.0000000|83486|64135183|
|2016-05-03 00:00:00.0000000|72247|13685621|

Cette requête doit être plus performante, car elle s’exécute sur une table plus petite. Dans cet exemple, la première requête s’exécute sur environ 215M enregistrements, tandis que la deuxième s’exécute sur seulement 32 enregistrements :

## <a name="example-intermediate-results"></a>Exemple : résultats intermédiaires

Requête de rétention.
Supposons que vous disposiez d’une table qui résume le moment où chaque page Wikipédia a été affichée (la taille de l’échantillon est de 10 m) et que vous souhaitez rechercher pour chaque date1 date2 le pourcentage de pages consultées dans date1 et date2 par rapport aux pages affichées à partir de date1 (date1 < date2).
  
La méthode la plus simple consiste à utiliser des opérateurs de jointure et de synthèse :

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

 
La requête ci-dessus a duré environ 18 secondes.

Lorsque vous utilisez les [`hll()`](hll-aggfunction.md) [`hll_merge()`](hll-merge-aggfunction.md) fonctions, et [`dcount_hll()`](dcount-hllfunction.md) , la requête équivalente se termine après environ 1,3 secondes et indique que les `hll` fonctions accélèrent la requête ci-dessus d’environ 14 fois :

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

|Day1|Day2|Pourcentage|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|33.2298494510578|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.9773830213667|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.5160020350006|

> [!NOTE] 
> Les résultats des requêtes ne sont pas 100% précis en raison de l’erreur des `hll` fonctions. Pour plus d’informations sur les erreurs, consultez [`dcount()`](dcount-aggfunction.md) .
