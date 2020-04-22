---
title: activity_metrics plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit activity_metrics plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 940320b7cd77ba09b31192853da9073511b33f5d
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030252"
---
# <a name="activity_metrics-plugin"></a>plug-in activity_metrics

Calcule les mesures d’activité utiles (valeurs de comptage distinctes, nombre distinct de nouvelles valeurs, taux de rétention et taux de désabonnement) en fonction de la fenêtre de période actuelle par rapport à la fenêtre de période précédente (contrairement [activity_counts_metrics plugin](activity-counts-metrics-plugin.md) dans lequel chaque fenêtre de temps est comparée à *toutes les* fenêtres de temps précédentes).

```kusto
T | evaluate activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**Syntaxe**

*T* `| evaluate` `,` *End*`,``,` `,` `,` *IdColumn* `,` `,` *Window* *Start* *dim1* *dim2* *TimelineColumn* IdColumn TimelineColumn [ Start End ] Fenêtre [ dim1 dim2 ...] `activity_metrics(``)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *IdColumn*: Le nom de la colonne avec des valeurs d’identification qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: Le nom de la colonne qui représente la chronologie.
* *Démarrer*: (facultatif) Scalar avec la valeur de la période de début d’analyse.
* *Fin*: (facultatif) Scalar avec valeur de la période de fin d’analyse.
* *Fenêtre*: Scalar avec la valeur de la période de fenêtre d’analyse. Peut être soit une valeur numérique /datetime/timestamp, `week` / `month` / `year`ou une chaîne qui est l’un des , auquel cas toutes les périodes seront [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md) en conséquence. 
* *dim1*, *dim2*, ...: (facultatif) liste des colonnes de dimensions qui tranchent le calcul des mesures d’activité.

**Retourne**

Retourne un tableau qui a les valeurs de comptage distinctes, le nombre distinct de nouvelles valeurs, le taux de rétention, et le taux de désabonnement pour chaque période de calendrier et pour chaque combinaison de dimensions existantes.

Le schéma de table de sortie est :

|*TimelineColumn (en)*|dcount_values|dcount_newvalues|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|--|--|--|--|--|--|
|type: à partir de *TimelineColumn*|long|long|double|double|..|..|..|

**Remarques**

***Définition du taux de rétention***

`Retention Rate`sur une période est calculée comme :

    # of customers returned during the period
    / (divided by)
    # customers at the beginning of the period

où `# of customers returned during the period` le est défini comme:

    # of customers at end of period
    - (minus)
    # of new customers acquired during the period

`Retention Rate`peut varier de 0,0 à 1,0  
Le score plus élevé signifie la plus grande quantité d’utilisateurs de retour.


***Définition du taux de churn***

`Churn Rate`sur une période est calculée comme :
    
    # of customers lost in the period
    / (divided by)
    # of customers at the beginning of the period

où `# of customer lost in the period` le est défini comme:

    # of customers at the beginning of the period
    - (minus)
    # of customers at the end of the period

`Churn Rate`peut varier de 0,0 à 1,0 Le score plus élevé signifie que le plus grand nombre d’utilisateurs ne retournent PAS au service.

***Taux de churn vs rétention***

Dérivé de la `Churn Rate` `Retention Rate`définition et , ce qui suit est toujours vrai:

    [Retention rate] = 100.0% - [Churn Rate]


**Exemples**

### <a name="weekly-retention-rate-and-churn-rate"></a>Taux de rétention hebdomadaire et taux de désabonnement

La requête suivante calcule le taux de rétention et de désabonnement pour la fenêtre d’une semaine à l’autre.

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
|2017-02-20 00:00:00.0000000|0.38|0.62|
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

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-churn-and-retention.png" border="false" alt-text="Mesures d’activité désabonnement et rétention":::

### <a name="distinct-values-and-distinct-new-values"></a>Valeurs distinctes et valeurs « nouvelles » distinctes 

La requête suivante calcule des valeurs distinctes et de « nouvelles » valeurs (ids qui n’apparaissaient pas dans la fenêtre de temps précédente) pour la fenêtre de semaine en semaine.


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

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-dcount-and-dcount-newvalues.png" border="false" alt-text="Les mesures d’activité dcount et dcount de nouvelles valeurs":::
