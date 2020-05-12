---
title: plug-in sliding_window_counts-Azure Explorateur de données
description: Cet article décrit sliding_window_counts plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2fbc870eafc45c8c63bea98a64f492d161af4c9b
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226344"
---
# <a name="sliding_window_counts-plugin"></a>plug-in sliding_window_counts

Calcule les nombres et le nombre de valeurs distinctes dans une fenêtre glissante sur une période lookback, à l’aide de la technique décrite [ici](samples.md#performing-aggregations-over-a-sliding-window).

Par exemple, pour chaque *jour*, calculer le nombre et le nombre de comptes des utilisateurs de la *semaine*précédente. 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

**Syntaxe**

*T* `| evaluate` `sliding_window_counts(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *end* `,` *LookbackWindow* `,` *bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

**Arguments**

* *T*: expression tabulaire d’entrée.
* *IdColumn*: nom de la colonne avec des valeurs d’ID qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: nom de la colonne représentant la chronologie.
* *Start*: scalaire avec la valeur de la période de démarrage de l’analyse.
* *End*: scalaire avec la valeur de la période de fin de l’analyse.
* *LookbackWindow*: valeur constante scalaire de la période lookback (par exemple, pour `dcount` les utilisateurs au cours des derniers 7D : LookbackWindow = 7D)
* *Bin*: valeur constante scalaire de la période de l’étape d’analyse. Cette valeur peut être une valeur numérique/DateTime/timestamp. Si la valeur est une chaîne au format `week` / `month` / `year` , tous les points seront [startOfWeek](startofweekfunction.md) / [StartOfMonth](startofmonthfunction.md) / [STARTOFYEAR](startofyearfunction.md). 
* *dim1*, *dim2*,... : (facultatif) liste des colonnes de dimensions qui découpent le calcul des métriques d’activité.

**Retourne**

Retourne une table qui contient les valeurs Count et distinct Count des ID dans la période lookback, pour chaque période de chronologie (par emplacement) et pour chaque combinaison de dimensions existante.

Le schéma de la table de sortie est le suivant :

|*TimelineColumn*|`dim1`|..|`dim_n`|`count`|`dcount`|
|---|---|---|---|---|---|
|type : à partir de *TimelineColumn*|..|..|..|long|long|


**Exemples**

Calculer les nombres et `dcounts` pour les utilisateurs de la semaine dernière, pour chaque jour de la période d’analyse. 

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

|Timestamp|Count|`dcount`|
|---|---|---|
|2017-08-01 00:00:00.0000000|5|3|
|2017-08-02 00:00:00.0000000|8|5|
|2017-08-03 00:00:00.0000000|13|5|
|2017-08-04 00:00:00.0000000|9|5|
|2017-08-05 00:00:00.0000000|7|5|
|2017-08-06 00:00:00.0000000|2|2|
|2017-08-07 00:00:00.0000000|1|1|