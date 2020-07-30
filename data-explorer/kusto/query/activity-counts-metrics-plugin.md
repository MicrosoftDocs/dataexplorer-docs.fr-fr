---
title: plug-in activity_counts_metrics-Azure Explorateur de données
description: Cet article décrit activity_counts_metrics plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: df7b994350297d911a0f3be59c791b6538899d67
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349786"
---
# <a name="activity_counts_metrics-plugin"></a>plug-in activity_counts_metrics

Calcule les métriques d’activité utiles pour chaque fenêtre de temps comparée/agrégée à *toutes les* fenêtres de temps précédentes. Les métriques sont les suivantes : nombre total de valeurs, valeurs de comptage de valeurs, nombre distinct de nouvelles valeurs et nombre de valeurs agrégées. Comparez ce plug-in [activity_metrics plug](activity-metrics-plugin.md)-in, dans lequel chaque fenêtre temporelle est comparée à sa fenêtre de temps précédente uniquement.

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>Syntaxe

*T* `| evaluate` `activity_counts_metrics(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *end* `,` *Window* [ `,` *cohorte*] [ `,` *dim1* `,` *dim2* `,` ...] [ `,` *lookback*]`)`

## <a name="arguments"></a>Arguments

* *T*: expression tabulaire d’entrée.
* *IdColumn*: nom de la colonne avec des valeurs d’ID qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: nom de la colonne qui représente la chronologie.
* *Start*: scalaire avec la valeur de la période de démarrage de l’analyse.
* *End*: scalaire avec la valeur de la période de fin de l’analyse.
* *Window*: scalaire avec la valeur de la période de la fenêtre d’analyse. Peut être une valeur numérique/DateTime/timestamp, ou une chaîne qui est l’une des `week` / `month` / `year` , auquel cas toutes les périodes sont [startOfWeek](startofweekfunction.md) / [StartOfMonth](startofmonthfunction.md) ou [STARTOFYEAR](startofyearfunction.md). 
* *dim1*, *dim2*,... : (facultatif) liste des colonnes de dimensions qui découpent le calcul des métriques d’activité.

## <a name="returns"></a>Retourne

Retourne une table qui contient : nombre total de valeurs, valeurs de comptage de valeurs, nombre distinct de nouvelles valeurs et nombre de valeurs agrégées pour chaque fenêtre de temps.

Le schéma de la table de sortie est le suivant :

|`TimelineColumn`|`dim1`|...|`dim_n`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`
|---|---|---|---|---|---|---|---|---|
|type : à partir de*`TimelineColumn`*|..|..|..|long|long|long|long|long


* *`TimelineColumn`*: Heure de début de la fenêtre de temps.
* *`count`*: Nombre total d’enregistrements dans la fenêtre de temps et *Dim (s)*
* *`dcount`*: Le nombre de valeurs d’ID distinctes dans la fenêtre de temps et les *Dim (s)*
* *`new_dcount`*: Valeurs d’ID distinctes dans la fenêtre de temps et *Dim (s)* comparées à toutes les fenêtres de temps précédentes. 
* *`aggregated_dcount`*: Valeurs d’ID distinctes agrégées totales de *Dim (s)* de la première fenêtre à la date actuelle (inclusive).

## <a name="examples"></a>Exemples

### <a name="daily-activity-counts"></a>Nombre d’activités quotidiennes 

La requête suivante calcule le nombre d’activités quotidiennes pour la table d’entrée fournie.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01), 
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),  
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
 T 
 | evaluate activity_counts_metrics(UserId, Timestamp, start, end, window)
```

|`Timestamp`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`|
|---|---|---|---|---|
|2017-08-01 00:00:00.0000000|4|4|4|4|
|2017-08-02 00:00:00.0000000|3|3|2|6|
|2017-08-03 00:00:00.0000000|6|5|2|8|
|2017-08-04 00:00:00.0000000|1|1|0|8|


