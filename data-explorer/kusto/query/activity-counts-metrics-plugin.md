---
title: activity_counts_metrics plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit activity_counts_metrics plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e55cd940850440499d227082f57769e499e6a551
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519310"
---
# <a name="activity_counts_metrics-plugin"></a>plug-in activity_counts_metrics

Calcule les mesures d’activité utiles (valeurs de comptage totales, valeurs de comptage distinctes, nombre distinct de nouvelles valeurs, nombre distinct agrégé) pour chaque fenêtre de temps comparée/agrégée à/avec *toutes les* fenêtres de temps précédentes (contrairement [activity_metrics plugin](activity-metrics-plugin.md) dans lequel chaque fenêtre de temps est comparée à sa fenêtre de temps précédente seulement).

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**Syntaxe**

*T* `| evaluate` `,` *Start* `,` *End* `,` *Window* `,` *Cohort*`,` `,` `,` *dim2* *dim1* *IdColumn* `,` *TimelineColumn* IdColumn TimelineColumn Start End Window [ Cohort ] [ dim1 dim2 ...] `activity_counts_metrics(` [`,` *Lookback*]`)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *IdColumn*: Le nom de la colonne avec des valeurs d’identification qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: Le nom de la colonne qui représente la chronologie.
* *Début*: Scalar avec la valeur de la période de début d’analyse.
* *Fin*: Scalar avec valeur de la période de fin d’analyse.
* *Fenêtre*: Scalar avec la valeur de la période de fenêtre d’analyse. Peut être soit une valeur numérique /datetime/timestamp, `week` / `month` / `year`ou une chaîne qui est l’un des , auquel cas toutes les périodes seront [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md) en conséquence. 
* *dim1*, *dim2*, ...: (facultatif) liste des colonnes de dimensions qui tranchent le calcul des mesures d’activité.

**Retourne**

Retourne un tableau qui a les valeurs de comptage totales, les valeurs de comptage distinctes, le nombre distinct de nouvelles valeurs, le nombre distinct agrégé pour chaque fenêtre de temps.

Le schéma de table de sortie est :

|TimelineColumn (en)|dim1|...|dim_n|count|dcount|new_dcount|aggregated_dcount
|---|---|---|---|---|---|---|---|---|
|type: à partir de *TimelineColumn*|..|..|..|long|long|long|long|long


* *TimelineColumn*: L’heure de début de la fenêtre.
* *compter*: Le nombre total d’enregistrements dans la fenêtre de temps et *dim(s)*
* *dcount*: Les valeurs d’identification distinctes comptent dans la fenêtre de temps et *dim(s)*
* *new_dcount*: Les valeurs d’identification distinctes dans la fenêtre de temps et *les dim(s)* par rapport à toutes les fenêtres de temps précédentes. 
* *aggregated_dcount*: Les valeurs d’identification distinctes agrégées totales de *dim(s)* de la fenêtre du 1er temps au courant (inclusive).

**Exemples**

### <a name="daily-activity-counts"></a>L’activité quotidienne compte 

La requête suivante calcule le nombre quotidien d’activités pour le tableau d’entrée fourni

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

|Timestamp|count|dcount|new_dcount|aggregated_dcount|
|---|---|---|---|---|
|2017-08-01 00:00:00.0000000|4|4|4|4|
|2017-08-02 00:00:00.0000000|3|3|2|6|
|2017-08-03 00:00:00.0000000|6|5|2|8|
|2017-08-04 00:00:00.0000000|1|1|0|8|


