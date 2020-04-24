---
title: rolling_percentile plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit rolling_percentile plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 02def4069c83eeec080ca059493132619fce30d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510266"
---
# <a name="rolling_percentile-plugin"></a>rolling_percentile plugin

Retourne une estimation pour le percentile spécifié de la population *ValueColumn* dans un roulement (glissement) *BinsPerWindow* fenêtre de taille par *BinSize*.

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

**Syntaxe**

*T* `| evaluate` `,` `,` `,` `,` `,` `,` *ValueColumn* `,` *Percentile* *dim1* *dim2* *BinSize* *IndexColumn* *BinsPerWindow* ValueColumn Percentile IndexColumn BinSize BinsPerWindow [ dim1 dim2 ...] `rolling_percentile(``)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *ValueColumn*: Le nom de la colonne avec des valeurs pour calculer le percentile de. 
* *Percentile*: Scalar avec le percentile à calculer.
* *IndexColumn*: Le nom de la colonne pour faire passer la fenêtre roulante.
* *BinSize*: Scalar avec la taille des bacs à appliquer sur *l’IndexColumn*.
* *BinsPerWindow*: Scalar avec le nombre de bacs inclus dans chaque fenêtre.
* *dim1*, *dim2*, ... : liste (facultatif) des colonnes de dimensions à trancher.

**Retourne**

Retourne une table avec une rangée par bac (et une combinaison de dimensions si spécifié) qui a le percentile roulant des valeurs dans la fenêtre se terminant au bac (inclusive). valeurs de comptage distinctes, nombre distinct de nouvelles valeurs, nombre distinct agrégé pour chaque fenêtre de temps.

Le schéma de table de sortie est :


|IndexColumn|dim1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


**Exemples**

### <a name="rolling-3-day-median-value-per-day"></a>Valeur médiane de 3 jours par jour 

La requête suivante calcule une valeur médiane de 3 jours dans la granularité quotidienne. Chaque rangée dans la sortie représente la valeur médiane pour les 3 derniers bacs (jours), y compris le bac lui-même.

```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3)
```

|Timestamp|rolling_3_percentile_val_50|
|---|---|
|2018-01-01 00:00:00.0000000|   12|
|2018-01-02 00:00:00.0000000|   24|
|2018-01-03 00:00:00.0000000|   36|
|2018-01-04 00:00:00.0000000|   60|
|2018-01-05 00:00:00.0000000|   84|
|2018-01-06 00:00:00.0000000|   108|
|2018-01-07 00:00:00.0000000|   132|
|2018-01-08 00:00:00.0000000|   156|
|2018-01-09 00:00:00.0000000|   180|
|2018-01-10 00:00:00.0000000|   204|

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>Roulement de la valeur médiane de 3 jours par jour par dimension

Même exemple d’en haut, mais calcule maintenant également la fenêtre roulante cloisonnée pour chaque valeur de la dimension.

```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3, EvenOrOdd)
```

|Timestamp| EvenOrOdd (en)|  rolling_3_percentile_val_50|
|---|---|---|
|2018-01-01 00:00:00.0000000|   Même|   12|
|2018-01-02 00:00:00.0000000|   Même|   24|
|2018-01-03 00:00:00.0000000|   Même|   36|
|2018-01-04 00:00:00.0000000|   Même|   60|
|2018-01-05 00:00:00.0000000|   Même|   84|
|2018-01-06 00:00:00.0000000|   Même|   108|
|2018-01-07 00:00:00.0000000|   Même|   132|
|2018-01-08 00:00:00.0000000|   Même|   156|
|2018-01-09 00:00:00.0000000|   Même|   180|
|2018-01-10 00:00:00.0000000|   Même|   204|
|2018-01-01 00:00:00.0000000|   Étrange|    11|
|2018-01-02 00:00:00.0000000|   Étrange|    23|
|2018-01-03 00:00:00.0000000|   Étrange|    35|
|2018-01-04 00:00:00.0000000|   Étrange|    59|
|2018-01-05 00:00:00.0000000|   Étrange|    83|
|2018-01-06 00:00:00.0000000|   Étrange|    107|
|2018-01-07 00:00:00.0000000|   Étrange|    131|
|2018-01-08 00:00:00.0000000|   Étrange|    155|
|2018-01-09 00:00:00.0000000|   Étrange|    179|
|2018-01-10 00:00:00.0000000|   Étrange|    203|