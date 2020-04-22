---
title: activity_engagement plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit activity_engagement plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fe6d3f68f59ae52c96d3e9467cc364d792b13cf3
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81664032"
---
# <a name="activity_engagement-plugin"></a>plug-in activity_engagement

calcule le taux d’engagement d’activité selon la colonne d’ID sur une fenêtre de chronologie glissante.

activity_engagement plugin peut être utilisé pour calculer DAU/WAU/MAU (activités quotidiennes/hebdomadaires/mensuelles).

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

**Syntaxe**

*T* `| evaluate` `,` *End*`,``,` `,` `,` `,` *IdColumn* `,` `,` *Start* *dim2* *dim1* *TimelineColumn* *OuterActivityWindow* *InnerActivityWindow* IdColumn TimelineColumn [ Start End ] InnerActivityWindow OuterActivityWindow [ dim1 dim2 ...] `activity_engagement(``)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *IdColumn*: Le nom de la colonne avec des valeurs d’identification qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: Le nom de la colonne qui représente la chronologie.
* *Démarrer*: (facultatif) Scalar avec la valeur de la période de début d’analyse.
* *Fin*: (facultatif) Scalar avec valeur de la période de fin d’analyse.
* *InnerActivityWindow*: Scalar avec la valeur de la période de fenêtre d’analyse de portée intérieure.
* *OuterActivityWindow*: Scalar avec la valeur de la période de fenêtre d’analyse de portée extérieure.
* *dim1*, *dim2*, ...: (facultatif) liste des colonnes de dimensions qui tranchent le calcul des mesures d’activité.

**Retourne**

Retourne une table qui a (compte distinct des valeurs d’identification à l’intérieur de la fenêtre de portée intérieure, compte distinct des valeurs d’identification à l’intérieur de la fenêtre de portée extérieure, et le rapport d’activité)pour chaque période de fenêtre de portée interne et pour chaque combinaison de dimensions existantes.

Le schéma de table de sortie est :

|TimelineColumn (en)|dcount_activities_inner|dcount_activities_outer|activity_ratio|dim1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|type: à partir de *TimelineColumn*|long|long|double|..|..|..|


**Exemples**

### <a name="dauwau-calculation"></a>Calcul DAU/WAU

L’exemple suivant calcule le rapport DAU/WAU (utilisateurs actifs quotidiens / utilisateurs actifs hebdomadaires) sur une donnée générée au hasard.

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/queries/activity-engagement-dau-wau.png" border="false" alt-text="Engagement d’activité dau wau":::

### <a name="daumau-calculation"></a>Calcul DAU/MAU

L’exemple suivant calcule le rapport DAU/WAU (utilisateurs actifs quotidiens / utilisateurs actifs hebdomadaires) sur une donnée générée au hasard.

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d)
| project _day, Dau_Mau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/queries/activity-engagement-dau-mau.png" border="false" alt-text="Activité engagement dau mau":::

### <a name="daumau-calculation-with-additional-dimensions"></a>Calcul DAU/MAU avec dimensions supplémentaires

L’exemple suivant calcule DAU/WAU (Ratio utilisateurs actifs quotidiens / utilisateurs actifs`mod3`hebdomadaires) sur une donnée générée au hasard avec une dimension supplémentaire ( ).

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
| extend mod3 = strcat("mod3=", id % 3)
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d, mod3)
| project _day, Dau_Mau=activity_ratio*100, mod3 
| render timechart 
```

:::image type="content" source="images/queries/activity-engagement-dau-mau-mod3.png" border="false" alt-text="Activité engagement dau mau mod 3":::
