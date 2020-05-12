---
title: plug-in activity_engagement-Azure Explorateur de données
description: Cet article décrit activity_engagement plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9aa85bcb12cd5f8d836f58ea9d16a318d8a40506
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225953"
---
# <a name="activity_engagement-plugin"></a>plug-in activity_engagement

calcule le taux d’engagement d’activité selon la colonne d’ID sur une fenêtre de chronologie glissante.

activity_engagement plug-in peut être utilisé pour le calcul de UAQ/WAU/MAU (activités quotidiennes/hebdomadaires/mensuelles).

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

**Syntaxe**

*T* `| evaluate` `activity_engagement(` *IdColumn* `,` *TimelineColumn* `,` [*Start* `,` *end* `,` ] *InnerActivityWindow* `,` *OuterActivityWindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

**Arguments**

* *T*: expression tabulaire d’entrée.
* *IdColumn*: nom de la colonne avec des valeurs d’ID qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: nom de la colonne qui représente la chronologie.
* *Start*: (facultatif) scalaire avec la valeur de la période de démarrage de l’analyse.
* *End*: (facultatif) scalaire avec la valeur de la période de fin de l’analyse.
* *InnerActivityWindow*: scalaire avec la valeur de la période de la fenêtre d’analyse de l’étendue interne.
* *OuterActivityWindow*: scalaire avec la valeur de la période de la fenêtre d’analyse de l’étendue externe.
* *dim1*, *dim2*,... : (facultatif) liste des colonnes de dimensions qui découpent le calcul des métriques d’activité.

**Retourne**

Retourne une table qui contient (nombre distinct de valeurs d’ID dans la fenêtre de l’étendue interne, le nombre de valeurs d’ID dans la fenêtre de l’étendue externe et le ratio d’activité) pour chaque période de la fenêtre de l’étendue interne et pour chaque combinaison de dimensions existante.

Le schéma de la table de sortie est le suivant :

|TimelineColumn|dcount_activities_inner|dcount_activities_outer|activity_ratio|dim1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|type : à partir de *TimelineColumn*|long|long|double|..|..|..|


**Exemples**

### <a name="dauwau-calculation"></a>Calcul UAQ/WAU

L’exemple suivant calcule UAQ/WAU (taux quotidien d’utilisateurs actifs/hebdomadaires actifs) sur des données générées de manière aléatoire.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-wau.png" border="false" alt-text="Engagement d’activité UAQ Wau":::

### <a name="daumau-calculation"></a>Calcul UAQ/MAU

L’exemple suivant calcule UAQ/WAU (taux quotidien d’utilisateurs actifs/hebdomadaires actifs) sur des données générées de manière aléatoire.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau.png" border="false" alt-text="Engagement d’activité UAQ Mau":::

### <a name="daumau-calculation-with-additional-dimensions"></a>Calcul UAQ/MAU avec dimensions supplémentaires

L’exemple suivant calcule UAQ/WAU (taux quotidien d’utilisateurs actifs/hebdomadaires actifs) sur des données générées de façon aléatoire avec une dimension supplémentaire ( `mod3` ).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau-mod3.png" border="false" alt-text="Engagement d’activité UAQ Mau mod 3":::
