---
title: plug-in activity_metrics-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit activity_metrics plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8106d419f20dcacdec6386294a5b9ffb8d1bc8e2
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225902"
---
# <a name="activity_metrics-plugin"></a>plug-in activity_metrics

Calcule les métriques d’activité utiles (valeurs de comptage de valeurs, nombre distinct de nouvelles valeurs, taux de rétention et taux d’évolution) en fonction de la fenêtre de période actuelle par rapport à la fenêtre de période précédente (contrairement à [activity_counts_metrics plug-in](activity-counts-metrics-plugin.md) dans lequel chaque fenêtre de temps est comparée à *toutes les* fenêtres de temps précédentes).

```kusto
T | evaluate activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**Syntaxe**

*T* `| evaluate` `activity_metrics(` *IdColumn* `,` *TimelineColumn* `,` [*Start* `,` *end* `,` ] *fenêtre* [ `,` *dim1* `,` *dim2* `,` ...]`)`

**Arguments**

* *T*: expression tabulaire d’entrée.
* *IdColumn*: nom de la colonne avec des valeurs d’ID qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: nom de la colonne qui représente la chronologie.
* *Start*: (facultatif) scalaire avec la valeur de la période de démarrage de l’analyse.
* *End*: (facultatif) scalaire avec la valeur de la période de fin de l’analyse.
* *Window*: scalaire avec la valeur de la période de la fenêtre d’analyse. Il peut s’agir d’une valeur numérique/DateTime/timestamp, ou d’une chaîne qui est l’une des `week` / `month` / `year` , auquel cas toutes les périodes sont [startOfWeek](startofweekfunction.md) / [StartOfMonth](startofmonthfunction.md) / [STARTOFYEAR](startofyearfunction.md) en conséquence. 
* *dim1*, *dim2*,... : (facultatif) liste des colonnes de dimensions qui découpent le calcul des métriques d’activité.

**Retourne**

Retourne une table qui contient les valeurs des nombres distincts, le nombre de nouvelles valeurs, le taux de rétention et le taux d’évolution pour chaque période de chronologie et pour chaque combinaison de dimensions existante.

Le schéma de la table de sortie est le suivant :

|*TimelineColumn*|dcount_values|dcount_newvalues|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|--|--|--|--|--|--|
|type : à partir de *TimelineColumn*|long|long|double|double|..|..|..|

**Remarques**

***Définition de la vitesse de rétention***

`Retention Rate`sur une période, est calculé comme suit :

    # of customers returned during the period
    / (divided by)
    # customers at the beginning of the period

où `# of customers returned during the period` est défini comme suit :

    # of customers at end of period
    - (minus)
    # of new customers acquired during the period

`Retention Rate`peut varier de 0,0 à 1,0  
Plus le score est élevé, plus le nombre d’utilisateurs renvoyés est important.


***Définition du taux d’évolution***

`Churn Rate`sur une période, est calculé comme suit :
    
    # of customers lost in the period
    / (divided by)
    # of customers at the beginning of the period

où `# of customer lost in the period` est défini comme suit :

    # of customers at the beginning of the period
    - (minus)
    # of customers at the end of the period

`Churn Rate`peut varier de 0,0 à 1,0 le score supérieur signifie que la plus grande quantité d’utilisateurs ne retourne pas au service.

***Évolution et taux de rétention***

Dérivée de la définition de `Churn Rate` et `Retention Rate` , les conditions suivantes sont toujours vraies :

    [Retention rate] = 100.0% - [Churn Rate]


**Exemples**

### <a name="weekly-retention-rate-and-churn-rate"></a>Taux de rétention hebdomadaire et taux d’évolution

La requête suivante calcule le taux de rétention et d’évolution pour la fenêtre de semaine sur semaine.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// 
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate, churn_rate
| render timechart 
```

|_day|retention_rate|churn_rate|
|---|---|---|
|2017-01-02 00:00:00.0000000|NaN|NaN|
|2017-01-09 00:00:00.0000000|0.179910044977511|0.820089955022489|
|2017-01-16 00:00:00.0000000|0.744374437443744|0.255625562556256|
|2017-01-23 00:00:00.0000000|0.612096774193548|0.387903225806452|
|2017-01-30 00:00:00.0000000|0.681141439205955|0.318858560794045|
|2017-02-06 00:00:00.0000000|0.278145695364238|0.721854304635762|
|2017-02-13 00:00:00.0000000|0.223172628304821|0.776827371695179|
|2017-02-20 00:00:00.0000000|0.38|0,62|
|2017-02-27 00:00:00.0000000|0.295519001701645|0.704480998298355|
|2017-03-06 00:00:00.0000000|0.280387770320656|0.719612229679344|
|2017-03-13 00:00:00.0000000|0.360628154795289|0.639371845204711|
|2017-03-20 00:00:00.0000000|0.288008028098344|0.711991971901656|
|2017-03-27 00:00:00.0000000|0.306134969325153|0.693865030674847|
|2017-04-03 00:00:00.0000000|0.356866537717602|0.643133462282398|
|2017-04-10 00:00:00.0000000|0.495098039215686|0.504901960784314|
|2017-04-17 00:00:00.0000000|0.198296836982968|0.801703163017032|
|2017-04-24 00:00:00.0000000|0.0618811881188119|0.938118811881188|
|2017-05-01 00:00:00.0000000|0.204657727593507|0.795342272406493|
|2017-05-08 00:00:00.0000000|0.517391304347826|0.482608695652174|
|2017-05-15 00:00:00.0000000|0.143667296786389|0.856332703213611|
|2017-05-22 00:00:00.0000000|0.199122325836533|0.800877674163467|
|2017-05-29 00:00:00.0000000|0.063468992248062|0.936531007751938|

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-churn-and-retention.png" border="false" alt-text="Évolution et rétention des mesures d’activité":::

### <a name="distinct-values-and-distinct-new-values"></a>Valeurs distinctes et valeurs « New » distinctes 

La requête suivante calcule les valeurs distinctes et les valeurs « nouvelles » (ID qui n’ont pas été affichées dans la fenêtre de temps précédente) pour la fenêtre de semaine sur semaine.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// 
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, dcount_values, dcount_newvalues
| render timechart 
```

|_day|dcount_values|dcount_newvalues|
|---|---|---|
|2017-01-02 00:00:00.0000000|630|630|
|2017-01-09 00:00:00.0000000|738|575|
|2017-01-16 00:00:00.0000000|1187|841|
|2017-01-23 00:00:00.0000000|1092|465|
|2017-01-30 00:00:00.0000000|1261|647|
|2017-02-06 00:00:00.0000000|1744|1043|
|2017-02-13 00:00:00.0000000|1563|432|
|2017-02-20 00:00:00.0000000|1406|818|
|2017-02-27 00:00:00.0000000|1956|1429|
|2017-03-06 00:00:00.0000000|1593|848|
|2017-03-13 00:00:00.0000000|1801|1423|
|2017-03-20 00:00:00.0000000|1710|1017|
|2017-03-27 00:00:00.0000000|1796|1516|
|2017-04-03 00:00:00.0000000|1381|1008|
|2017-04-10 00:00:00.0000000|1756|1162|
|2017-04-17 00:00:00.0000000|1831|1409|
|2017-04-24 00:00:00.0000000|1823|1164|
|2017-05-01 00:00:00.0000000|1811|1353|
|2017-05-08 00:00:00.0000000|1691|1246|
|2017-05-15 00:00:00.0000000|1812|1608|
|2017-05-22 00:00:00.0000000|1740|1017|
|2017-05-29 00:00:00.0000000|960|756|

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-dcount-and-dcount-newvalues.png" border="false" alt-text="Métriques d’activité DCount et DCount nouvelles valeurs":::
