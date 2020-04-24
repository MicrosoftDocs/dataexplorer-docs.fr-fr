---
title: dcount_intersect plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit dcount_intersect plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7771456ffa75085c79933c2e789e3d98f352b76f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516131"
---
# <a name="dcount_intersect-plugin"></a>dcount_intersect plugin

Calcule l’intersection entre les ensembles N en fonction des valeurs de hll (N dans la gamme de [2.16]), et retourne les valeurs N dcount.

Compte tenu des ensembles S<sub>1</sub>, S<sub>2</sub>, .. S<sub>n</sub> - les valeurs de rendement représenteront des nombres distincts de :  
S<sub>1</sub>, S<sub>1</sub> S<sub>2</sub>,  
S<sub>1</sub> S<sub>2</sub> S<sub>3</sub>,  
... ,  
S<sub>1</sub> S<sub>2</sub> ... S<sub>n</sub>

    T | evaluate dcount_intersect(hll_1, hll_2, hll_3)

**Syntaxe**

*T* `| evaluate` T `dcount_intersect(` *hll_1*, *hll_2*, [`,` *hll_3* `,` ...]`)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *hll_i*: les valeurs de l’ensemble S<sub>i</sub> calculé avec la fonction [hll.)](./hll-aggfunction.md)

**Retourne**

Retourne une table avec des valeurs N dcount (par colonnes de colonne, représentant les intersections des ensembles).
Les noms de colonne sont s0, s1, ... (jusqu’à n-1).

**Exemples**

```kusto
// Generate numbers from 1 to 100
range x from 1 to 100 step 1
| extend isEven = (x % 2 == 0), isMod3 = (x % 3 == 0), isMod5 = (x % 5 == 0)
// Calculate conditional HLL values (note that '0' is included in each of them as additional value, so we will subtract it later)
| summarize hll_even = hll(iif(isEven, x, 0), 2),
            hll_mod3 = hll(iif(isMod3, x, 0), 2),
            hll_mod5 = hll(iif(isMod5, x, 0), 2) 
// Invoke the plugin that calculates dcount intersections         
| evaluate dcount_intersect(hll_even, hll_mod3, hll_mod5)
| project evenNumbers = s0 - 1,             //                             100 / 2 = 50
          even_and_mod3 = s1 - 1,           // gcd(2,3) = 6, therefor:     100 / 6 = 16
          even_and_mod3_and_mod5 = s2 - 1   // gcd(2,3,5) is 30, therefore: 100 / 30 = 3 
```

|même Les membres|even_and_mod3|even_and_mod3_and_mod5|
|---|---|---|
|50|16|3|