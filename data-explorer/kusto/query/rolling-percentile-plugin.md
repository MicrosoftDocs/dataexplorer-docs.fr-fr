---
title: plug-in rolling_percentile-Azure Explorateur de données
description: Cet article décrit rolling_percentile plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a41a45fb12fafe62fffd6c13e5ea9ecff55bb355
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373009"
---
# <a name="rolling_percentile-plugin"></a>plug-in rolling_percentile

Retourne une estimation pour le centile spécifié de la population *ValueColumn* dans une fenêtre de taille de *BinsPerWindow* enchaînée (coulissante) par *emplacement*.

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

**Syntaxe**

*T* `| evaluate` `rolling_percentile(` *ValueColumn* `,` *centile* `,` *IndexColumn* `,` *Corbeille* `,` *BinsPerWindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

**Arguments**

* *T*: expression tabulaire d’entrée.
* *ValueColumn*: nom de la colonne dont les valeurs calculent le centile. 
* *Centile*: scalaire avec le centile à calculer.
* *IndexColumn*: nom de la colonne sur laquelle exécuter la fenêtre dynamique.
* *Corbeille*: scalaire avec la taille des emplacements à appliquer sur le *IndexColumn*.
* *BinsPerWindow*: scalaire avec nombre d’emplacements inclus dans chaque fenêtre.
* *dim1*, *dim2*,... : (facultatif) liste des colonnes de dimensions à découper.

**Retourne**

Retourne une table avec une ligne pour chaque emplacement (et une combinaison de dimensions, si elle est spécifiée) qui a le centile enchaîné de valeurs dans la fenêtre se terminant par le chuton (inclus). valeurs de comptage de valeurs, nombre distinct de nouvelles valeurs, compte distinct agrégé pour chaque fenêtre de temps.

Le schéma de la table de sortie est le suivant :


|IndexColumn|dim1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


**Exemples**

### <a name="rolling-3-day-median-value-per-day"></a>Répercussion de la valeur moyenne de 3 jours par jour 

La requête suivante calcule une valeur médiane de 3 jours en granularité quotidienne. Chaque ligne de la sortie représente la valeur moyenne des 3 derniers casiers (jours), y compris le compartiment lui-même.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>Répercussion d’une valeur médiane de 3 jours par jour par dimension

Même exemple que ci-dessus, mais calcule désormais également la fenêtre propagée partitionnée pour chaque valeur de la dimension.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3, EvenOrOdd)
```

|Timestamp| EvenOrOdd|  rolling_3_percentile_val_50|
|---|---|---|
|2018-01-01 00:00:00.0000000|   Espacé|   12|
|2018-01-02 00:00:00.0000000|   Espacé|   24|
|2018-01-03 00:00:00.0000000|   Espacé|   36|
|2018-01-04 00:00:00.0000000|   Espacé|   60|
|2018-01-05 00:00:00.0000000|   Espacé|   84|
|2018-01-06 00:00:00.0000000|   Espacé|   108|
|2018-01-07 00:00:00.0000000|   Espacé|   132|
|2018-01-08 00:00:00.0000000|   Espacé|   156|
|2018-01-09 00:00:00.0000000|   Espacé|   180|
|2018-01-10 00:00:00.0000000|   Espacé|   204|
|2018-01-01 00:00:00.0000000|   Optique|    11|
|2018-01-02 00:00:00.0000000|   Optique|    23|
|2018-01-03 00:00:00.0000000|   Optique|    35|
|2018-01-04 00:00:00.0000000|   Optique|    59|
|2018-01-05 00:00:00.0000000|   Optique|    83|
|2018-01-06 00:00:00.0000000|   Optique|    107|
|2018-01-07 00:00:00.0000000|   Optique|    131|
|2018-01-08 00:00:00.0000000|   Optique|    155|
|2018-01-09 00:00:00.0000000|   Optique|    179|
|2018-01-10 00:00:00.0000000|   Optique|    203|
