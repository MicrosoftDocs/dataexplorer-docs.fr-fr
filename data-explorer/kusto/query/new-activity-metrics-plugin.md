---
title: new_activity_metrics plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit new_activity_metrics plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0aad1c91fec6855030544596a08818b80bbf3d18
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512204"
---
# <a name="new_activity_metrics-plugin"></a>plug-in new_activity_metrics

Calcule les mesures d’activité utiles (valeurs de comptage distinctes, nombre distinct `New Users`de nouvelles valeurs, taux de rétention et taux de désabonnement) pour la cohorte de . Chaque cohorte (tous `New Users` les utilisateurs qui étaient 1er vu dans la fenêtre de temps) est comparée à toutes les cohortes antérieures. La comparaison tient compte de *toutes les* fenêtres de temps précédentes. Par exemple, dans le dossier pour à partir de T2 et à T3, le nombre distinct d’utilisateurs sera tous les utilisateurs dans T3 qui n’ont pas été vus dans T1 et T2. 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**Syntaxe**

*T* `| evaluate` `,` *Start* `,` *End* `,` *Window* `,` *Cohort*`,` `,` `,` *dim2* *dim1* *IdColumn* `,` *TimelineColumn* IdColumn TimelineColumn Start End Window [ Cohort ] [ dim1 dim2 ...] `new_activity_metrics(` [`,` *Lookback*]`)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *IdColumn*: Le nom de la colonne avec des valeurs d’identification qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: Le nom de la colonne qui représente la chronologie.
* *Début*: Scalar avec la valeur de la période de début d’analyse.
* *Fin*: Scalar avec valeur de la période de fin d’analyse.
* *Fenêtre*: Scalar avec la valeur de la période de fenêtre d’analyse. Peut être soit une valeur numérique /datetime/timestamp, `week` / `month` / `year`ou une chaîne qui est l’un des , auquel cas toutes les périodes seront [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md) en conséquence. 
* *Cohorte*: (facultatif) une constante scalaire indiquant une cohorte spécifique. Si elles ne sont pas fournies, toutes les cohortes correspondant à la fenêtre de temps d’analyse sont calculées et retournées.
* *dim1*, *dim2*, ...: (facultatif) liste des colonnes de dimensions qui tranchent le calcul des mesures d’activité.
* *Lookback*: (facultatif) une expression tabulaire avec un ensemble d’IDs qui appartiennent à la période «regarder en arrière»

**Retourne**

Retourne un tableau qui a les valeurs de comptage distinctes, le nombre distinct de nouvelles valeurs, le taux de rétention, et le taux de désabonnement pour chaque combinaison de «de» et «à» périodes de calendrier et pour chaque combinaison de dimensions existantes.

Le schéma de table de sortie est :

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|type: à partir de *TimelineColumn*|identique|long|long|double|double|double|..|..|..|

* `from_TimelineColumn`- la cohorte de nouveaux utilisateurs. Les mesures de cet enregistrement se réfèrent à tous les utilisateurs qui ont été vus pour la première fois au cours de cette période. La décision sur *la première vue* tient compte de toutes les périodes précédentes de la période d’analyse. 
* `to_TimelineColumn`- la période étant comparée à. 
* `dcount_new_values`- le nombre d’utilisateurs distincts dans `to_TimelineColumn` lesquels n’ont pas été vus dans toutes *les* périodes avant et y compris `from_TimelineColumn`. 
* `dcount_retained_values`- de tous les nouveaux `from_TimelineColumn`utilisateurs, vu pour la première `to_TimelineCoumn`fois en , le nombre d’utilisateurs distincts qui ont été vus dans .
* `dcount_churn_values`- de tous les nouveaux `from_TimelineColumn`utilisateurs, vu pour la première `to_TimelineCoumn`fois en , le nombre d’utilisateurs distincts qui *n’ont pas* été vus dans .
* `retention_rate`- le `dcount_retained_values` pourcentage de hors de la `from_TimelineColumn`cohorte (les utilisateurs d’abord vu dans ).
* `churn_rate`- le `dcount_churn_values` pourcentage de hors de la `from_TimelineColumn`cohorte (les utilisateurs d’abord vu dans ).

**Remarques**

Pour les `Retention Rate` définitions de et `Churn Rate` - se référer à la section **Notes** dans activity_metrics la documentation [plugin.](./activity-metrics-plugin.md)


**Exemples**

L’ensemble de données d’échantillon suivant montre quels utilisateurs ont vu les jours. Le tableau a été `Users` généré sur la base d’un tableau source, comme suit: 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|Timestamp|set_user|
|---|---|
|2019-11-01 00:00:00.0000000|[0,2,3,4]|
|2019-11-02 00:00:00.0000000|[0,1,3,4,5]|
|2019-11-03 00:00:00.0000000|[0,2,4,5]|
|2019-11-04 00:00:00.0000000|[0,1,2,3]|
|2019-11-05 00:00:00.0000000|[0,1,2,3,4]|

La sortie du plugin pour la table d’origine est la suivante: 

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

Voici une analyse de quelques enregistrements de la sortie : 
* Record `R=3` `from_TimelineColumn`  =  `2019-11-01`, `to_TimelineColumn`  = , `2019-11-03`:
    * Les utilisateurs considérés pour cet enregistrement sont tous de nouveaux utilisateurs vus sur 11/1. Comme il s’agit de la première période, ce sont tous des utilisateurs dans ce bac [0,2,3,4]
    * `dcount_new_values`le nombre d’utilisateurs sur 11/3 qui n’ont pas été vus sur 11/1. Cela comprend un `5`seul utilisateur . 
    * `dcount_retained_values`- parmi tous les nouveaux utilisateurs sur 11/1, combien ont été retenus jusqu’à 11/3? Il y`[0,2,4]`en `count_churn_values` a trois (), tandis qu’il en est un`3`(utilisateur). 
    * `retention_rate`0,75 - les trois utilisateurs retenus sur les quatre nouveaux utilisateurs qui ont été vus pour la première fois en 11/1. 

* Record `R=9` `from_TimelineColumn`  =  `2019-11-02`, `to_TimelineColumn`  = , `2019-11-04`:
    * Cet enregistrement se concentre sur les nouveaux utilisateurs qui ont `1` été `5`vus pour la première fois sur 1 1/2 - utilisateurs et . 
    * `dcount_new_values`- le nombre d’utilisateurs sur 11/4 qui `T0 .. from_Timestamp`n’ont pas été vus à travers toutes les périodes . Signification, les utilisateurs qui sont vus sur 11/4, mais qui n’ont pas été vus sur soit 11/1 ou 11/2 - il n’y a pas de tels utilisateurs. 
    * `dcount_retained_values`- de tous les nouveaux utilisateurs sur`[1,5]`11/2 ( ), combien ont été retenus jusqu’à 11/4? Il ya un tel`[1]`utilisateur ( ), tandis que `5`count_churn_values est un (utilisateur ). 
    * `retention_rate`est de 0,5 - l’utilisateur unique qui a été retenu sur 11/4 sur les deux nouveaux sur 11/2. 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>Taux de rétention hebdomadaire et taux de désabonnement (semaine unique)

La requête suivante calcule un taux de rétention et `New Users` de désabonnement pour la fenêtre d’une semaine à l’autre pour la cohorte (utilisateurs qui sont arrivés la première semaine).

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


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>Taux de rétention hebdomadaire et taux de désabonnement (matrice complète)

La requête suivante calcule le taux de rétention et `New Users` de désabonnement pour la fenêtre d’une semaine à l’autre pour la cohorte. Si l’exemple précédent a calculé les statistiques pour une seule semaine - ce qui ci-dessous produit le tableau NxN pour chaque combinaison.

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


### <a name="weekly-retention-rate-with-lookback-period"></a>Taux de rétention hebdomadaire avec période de retour

La requête suivante calcule le `New Users` taux de `lookback` rétention de la cohorte en tenant compte de la `New Users` période : une requête tabulaire avec `New Users`un ensemble d’ids qui sont utilisés pour définir la cohorte (toutes les pièces d’identité qui n’apparaissent pas dans cet ensemble sont ). La requête examine le comportement `New Users` de rétention de la période d’analyse.

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
