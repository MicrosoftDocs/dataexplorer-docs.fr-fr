---
title: plug-in new_activity_metrics-Azure Explorateur de données
description: Cet article décrit new_activity_metrics plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 447191158ce3de69c4429b5af4a33d24150af77c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248860"
---
# <a name="new_activity_metrics-plugin"></a>plug-in new_activity_metrics

Calcule les métriques d’activité utiles (valeurs de comptage de valeurs, nombre distinct de nouvelles valeurs, taux de rétention et taux d’évolution) pour la cohorte de `New Users` . Chaque cohorte de `New Users` (tous les utilisateurs qui ont été affichés dans la fenêtre de temps) est comparée à toutes les cohortes antérieures. La comparaison prend en compte *toutes les* fenêtres de temps précédentes. Par exemple, dans l’enregistrement pour from = T2 et to = T3, le nombre d’utilisateurs distincts sera tous les utilisateurs de T3 qui n’étaient pas vus à la fois dans T1 et T2. 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>Syntaxe

*T* `| evaluate` `new_activity_metrics(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *end* `,` *Window* [ `,` *cohorte*] [ `,` *dim1* `,` *dim2* `,` ...] [ `,` *lookback*]`)`

## <a name="arguments"></a>Arguments

* *T*: expression tabulaire d’entrée.
* *IdColumn*: nom de la colonne avec des valeurs d’ID qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: nom de la colonne qui représente la chronologie.
* *Start*: scalaire avec la valeur de la période de démarrage de l’analyse.
* *End*: scalaire avec la valeur de la période de fin de l’analyse.
* *Window*: scalaire avec la valeur de la période de la fenêtre d’analyse. Il peut s’agir d’une valeur numérique/DateTime/timestamp, ou d’une chaîne qui est l’une des `week` / `month` / `year` , auquel cas toutes les périodes sont [startOfWeek](startofweekfunction.md) / [StartOfMonth](startofmonthfunction.md) / [STARTOFYEAR](startofyearfunction.md) en conséquence. 
* *Cohorte*: (facultatif) constante scalaire indiquant une cohorte spécifique. S’il n’est pas fourni, toutes les cohortes correspondant à la fenêtre de temps de l’analyse sont calculées et retournées.
* *dim1*, *dim2*,... : (facultatif) liste des colonnes de dimensions qui découpent le calcul des métriques d’activité.
* *Lookback*: (facultatif) une expression tabulaire avec un ensemble d’ID qui appartiennent à la période « Look Back »

## <a name="returns"></a>Retours

Retourne une table qui contient les valeurs de nombres distincts, le nombre de nouvelles valeurs, le taux de rétention et le taux d’évolution pour chaque combinaison de périodes de chronologie « de » et « à » et pour chaque combinaison de dimensions existante.

Le schéma de la table de sortie est le suivant :

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|type : à partir de *TimelineColumn*|identique|long|long|double|double|double|..|..|..|

* `from_TimelineColumn` -la cohorte de nouveaux utilisateurs. Les métriques de cet enregistrement font référence à tous les utilisateurs qui ont été vus pour la première fois au cours de cette période. La décision sur la *première vue* prend en compte toutes les périodes précédentes de la période d’analyse. 
* `to_TimelineColumn` -période comparée à. 
* `dcount_new_values` -nombre d’utilisateurs distincts dans `to_TimelineColumn` lesquels n’étaient pas visibles dans *toutes les* périodes antérieures à et y compris `from_TimelineColumn` . 
* `dcount_retained_values` -en dehors de tous les nouveaux utilisateurs, vus tout d’abord dans `from_TimelineColumn` , le nombre d’utilisateurs distincts qui ont été vus dans `to_TimelineCoumn` .
* `dcount_churn_values` -en dehors de tous les nouveaux utilisateurs, vus tout d’abord dans `from_TimelineColumn` , le nombre d’utilisateurs distincts qui n’étaient *pas* vus dans `to_TimelineCoumn` .
* `retention_rate` -pourcentage de `dcount_retained_values` la cohorte (utilisateurs affichés en premier dans `from_TimelineColumn` ).
* `churn_rate` -pourcentage de `dcount_churn_values` la cohorte (utilisateurs affichés en premier dans `from_TimelineColumn` ).

**Notes**

Pour obtenir les définitions de `Retention Rate` et `Churn Rate` , consultez la section **Notes** dans activity_metrics documentation du [plug-in](./activity-metrics-plugin.md) .


## <a name="examples"></a>Exemples

L’exemple de jeu de données suivant montre quels utilisateurs ont vu quels jours. La table a été générée à partir d’une `Users` table source, comme suit : 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|Timestamp|set_user|
|---|---|
|2019-11-01 00:00:00.0000000|[0, 2, 3, 4]|
|2019-11-02 00:00:00.0000000|[0, 1, 3, 4, 5]|
|2019-11-03 00:00:00.0000000|[0, 2, 4, 5]|
|2019-11-04 00:00:00.0000000|[0, 1, 2, 3]|
|2019-11-05 00:00:00.0000000|[0, 1, 2, 3,4]|

La sortie du plug-in pour la table d’origine est la suivante : 

```kusto
let StartDate = datetime(2019-11-01 00:00:00);
let EndDate = datetime(2019-11-07 00:00:00);
Users 
| evaluate new_activity_metrics(user, Timestamp, StartDate, EndDate-1tick, 1d) 
| where from_Timestamp < datetime(2019-11-03 00:00:00.0000000)
```

|R|from_Timestamp|to_Timestamp|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|
|---|---|---|---|---|---|---|---|
|1|2019-11-01 00:00:00.0000000|2019-11-01 00:00:00.0000000|4|4|0|1|0|
|2|2019-11-01 00:00:00.0000000|2019-11-02 00:00:00.0000000|2|3|1|0,75|0,25|
|3|2019-11-01 00:00:00.0000000|2019-11-03 00:00:00.0000000|1|3|1|0,75|0,25|
|4|2019-11-01 00:00:00.0000000|2019-11-04 00:00:00.0000000|1|3|1|0,75|0,25|
|5|2019-11-01 00:00:00.0000000|2019-11-05 00:00:00.0000000|1|4|0|1|0|
|6|2019-11-01 00:00:00.0000000|2019-11-06 00:00:00.0000000|0|0|4|0|1|
|7|2019-11-02 00:00:00.0000000|2019-11-02 00:00:00.0000000|2|2|0|1|0|
|8|2019-11-02 00:00:00.0000000|2019-11-03 00:00:00.0000000|0|1|1|0.5|0.5|
|9|2019-11-02 00:00:00.0000000|2019-11-04 00:00:00.0000000|0|1|1|0.5|0.5|
|10|2019-11-02 00:00:00.0000000|2019-11-05 00:00:00.0000000|0|1|1|0.5|0.5|
|11|2019-11-02 00:00:00.0000000|2019-11-06 00:00:00.0000000|0|0|2|0|1|

Voici une analyse de quelques enregistrements de la sortie : 
* Enregistrement `R=3` , `from_TimelineColumn`  =  `2019-11-01` , `to_TimelineColumn`  =  `2019-11-03` :
    * Les utilisateurs pris en compte pour cet enregistrement sont tous les nouveaux utilisateurs qui se sont vus sur 11/1. Étant donné qu’il s’agit de la première période, il s’agit de tous les utilisateurs de cet emplacement ([0, 2, 3, 4])
    * `dcount_new_values` : nombre d’utilisateurs sur 11/3 qui n’ont pas été vus sur 11/1. Cela comprend un seul utilisateur : `5` . 
    * `dcount_retained_values` : en dehors de tous les nouveaux utilisateurs sur 11/1, combien ont été conservés jusqu’à 11/3 ? Il existe trois ( `[0,2,4]` ), tandis que `count_churn_values` est un (utilisateur = `3` ). 
    * `retention_rate` = 0,75 – les trois utilisateurs sont conservés parmi les quatre nouveaux utilisateurs qui ont été vus pour la première fois dans 11/1. 

* Enregistrement `R=9` , `from_TimelineColumn`  =  `2019-11-02` , `to_TimelineColumn`  =  `2019-11-04` :
    * Cet enregistrement porte sur les nouveaux utilisateurs qui ont été vus pour la première fois sur 11/2 – utilisateurs `1` et `5` . 
    * `dcount_new_values` : nombre d’utilisateurs sur 11/4 qui n’ont pas été vus sur toutes les périodes `T0 .. from_Timestamp` . Autrement dit, les utilisateurs qui sont vus sur 11/4 mais qui n’ont pas été vus sur 11/1 ou 11/2, il n’y a pas d’utilisateurs de ce type. 
    * `dcount_retained_values` : en dehors de tous les nouveaux utilisateurs sur 11/2 ( `[1,5]` ), combien ont été conservés jusqu’à 11/4 ? Il y a un utilisateur de ce type ( `[1]` ), tandis que count_churn_values est un (utilisateur `5` ). 
    * `retention_rate` est 0,5 – l’utilisateur unique qui a été conservé le 11/4 des deux nouveaux sur 11/2. 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>Taux de rétention hebdomadaire et taux d’évolution (une seule semaine)

La requête suivante calcule un taux de rétention et d’évolution pour la période de semaine-semaine pour la `New Users` cohorte (utilisateurs arrivés la première semaine).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end  step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

|from_Day|to_Day|retention_rate|churn_rate|
|---|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|0|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.544632768361582|0.455367231638418|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.031638418079096|0.968361581920904|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>Taux de rétention hebdomadaire et taux d’évolution (matrice complète)

La requête suivante calcule le taux de rétention et d’évolution pour la période de semaine-semaine pour la `New Users` cohorte. Si l’exemple précédent a calculé les statistiques pour une seule semaine, le tableau ci-dessous génère une table NxN pour chaque combinaison de/à.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end  step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Last parameter is omitted - 
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d)
| project from_Day, to_Day, retention_rate, churn_rate
```

|from_Day|to_Day|retention_rate|churn_rate|
|---|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|0|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.190397350993377|0.809602649006622|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|
|2017-05-08 00:00:00.0000000|2017-05-08 00:00:00.0000000|1|0|
|2017-05-08 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.405263157894737|0.594736842105263|
|2017-05-08 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.227631578947368|0.772368421052632|
|2017-05-08 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|
|2017-05-15 00:00:00.0000000|2017-05-15 00:00:00.0000000|1|0|
|2017-05-15 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.785488958990536|0.214511041009464|
|2017-05-15 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.237644584647739|0.762355415352261|
|2017-05-22 00:00:00.0000000|2017-05-22 00:00:00.0000000|1|0|
|2017-05-22 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.621835443037975|0.378164556962025|
|2017-05-29 00:00:00.0000000|2017-05-29 00:00:00.0000000|1|0|


### <a name="weekly-retention-rate-with-lookback-period"></a>Taux de rétention hebdomadaire avec période de lookback

La requête suivante calcule le taux de rétention de la `New Users` cohorte lors de la prise en compte de la `lookback` période : une requête tabulaire avec un jeu d’ID utilisé pour définir la `New Users` cohorte (tous les ID qui n’apparaissent pas dans cet ensemble sont `New Users` ). La requête examine le comportement de rétention de `New Users` pendant la période d’analyse.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _lookback = datetime(2017-02-01);
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
let _data = range Day from _lookback to _end  step 1d
| extend d = tolong((Day - _lookback)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000;
//
let lookback_data = _data | where Day < _start | project Day, id;
_data
| evaluate new_activity_metrics(id, Day, _start, _end, 7d, _start, lookback_data)
| project from_Day, to_Day, retention_rate
```

|from_Day|to_Day|retention_rate|
|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.404081632653061|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.257142857142857|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.296326530612245|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.0587755102040816|
