---
title: sliding_window_counts plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit sliding_window_counts plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: feab3d0e8f548817be12f202eb2d494bd65aa133
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507495"
---
# <a name="sliding_window_counts-plugin"></a>sliding_window_counts plugin

Calcule les comptes et le nombre distinct de valeurs dans une fenêtre coulissante sur une période de retour, en utilisant la technique décrite [ici](samples.md#performing-aggregations-over-a-sliding-window).

Par exemple, pour chaque *jour,* calculer le nombre et le nombre distinct d’utilisateurs dans la *semaine*précédente . 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

**Syntaxe**

*T* `| evaluate` `,` *Start* `,` *End* `,` `,` *Bin* `,` `,` *dim2* `,` *IdColumn* `,` *TimelineColumn* *dim1* *LookbackWindow* IdColumn TimelineColumn Start End LookbackWindow Bin [ dim1 dim2 ...] `sliding_window_counts(``)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *IdColumn*: Le nom de la colonne avec des valeurs d’identification qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: Le nom de la colonne qui représente la chronologie.
* *Début*: Scalar avec la valeur de la période de début d’analyse.
* *Fin*: Scalar avec valeur de la période de fin d’analyse.
* *LookbackWindow*: Valeur constante Scalar de la période de retour (p. ex. pour les utilisateurs de dcount dans le passé 7d: LookbackWindow - 7d)
* *Bin*: Valeur constanct Scalar de la période d’étape d’analyse. Peut être soit une valeur numérique /datetime/timestamp, `week` / `month` / `year`ou une chaîne qui est l’un des , auquel cas toutes les périodes seront [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md) en conséquence. 
* *dim1*, *dim2*, ...: (facultatif) liste des colonnes de dimensions qui tranchent le calcul des mesures d’activité.

**Retourne**

Retourne un tableau qui a le nombre et les valeurs de comptage distinctes des Ids dans la période de retour, pour chaque période de chronologie (par bac) et pour chaque combinaison de dimensions existantes.

Le schéma de table de sortie est :

|*TimelineColumn (en)*|dim1|..|dim_n|Count|Dcount (Dcount)|
|---|---|---|---|---|---|
|type: à partir de *TimelineColumn*|..|..|..|long|long|


**Exemples**

Calculez les dénombrements et les dcomptes pour les utilisateurs au cours de la semaine dernière, pour chaque jour dans la période d’analyse. 

```kusto
let start = datetime(2017 - 08 - 01);
let end = datetime(2017 - 08 - 07); 
let lookbackWindow = 3d;  
let bin = 1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'Bob',      datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'John',     datetime(2017 - 08 - 01), 
'Bob',      datetime(2017 - 08 - 01), 
'Ananda',   datetime(2017 - 08 - 02),  
'Atul',     datetime(2017 - 08 - 02), 
'John',     datetime(2017 - 08 - 02), 
'Ananda',   datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'John',     datetime(2017 - 08 - 03), 
'Bob',      datetime(2017 - 08 - 03), 
'Betsy',    datetime(2017 - 08 - 04), 
'Bob',      datetime(2017 - 08 - 05), 
];
T | evaluate sliding_window_counts(UserId, Timestamp, start, end, lookbackWindow, bin)


```

|Timestamp|Count|Dcount (Dcount)|
|---|---|---|
|2017-08-01 00:00:00.0000000|5|3|
|2017-08-02 00:00:00.0000000|8|5|
|2017-08-03 00:00:00.0000000|13|5|
|2017-08-04 00:00:00.0000000|9|5|
|2017-08-05 00:00:00.0000000|7|5|
|2017-08-06 00:00:00.0000000|2|2|
|2017-08-07 00:00:00.0000000|1|1|