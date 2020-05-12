---
title: plug-in dcount_intersect-Azure Explorateur de données
description: Cet article décrit dcount_intersect plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c431f17184570b294b9c8077028ac792719b4abd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225205"
---
# <a name="dcount_intersect-plugin"></a>plug-in dcount_intersect

Calcule l’intersection entre N Jeux en fonction des `hll` valeurs (N dans la plage de [2.. 16]) et retourne n `dcount` valeurs.

Jeux donnés S<sub>1</sub>, s<sub>2</sub>,.. S<sub>n</sub> -retourne des valeurs qui représenteront des nombres distincts de :  
S<sub>1</sub>, s<sub>1</sub> ∩ s<sub>2</sub>,  
S<sub>1</sub> ∩ s<sub>2</sub> ∩ s<sub>3</sub>,  
... ,  
S<sub>1</sub> ∩ s<sub>2</sub> ∩... ∩ S<sub>n</sub>

    T | evaluate dcount_intersect(hll_1, hll_2, hll_3)

**Syntaxe**

*T* `| evaluate` `dcount_intersect(` *hll_1*, *hll_2*, [ `,` *hll_3* `,` ...]`)`

**Arguments**

* *T*: expression tabulaire d’entrée.
* *hll_i*: valeurs de Set S<sub>i</sub> calculées à l’aide de la [`hll()`](./hll-aggfunction.md) fonction.

**Retourne**

Retourne une table avec N `dcount` valeurs (par colonne, représentant les intersections de jeu).
Les noms de colonnes sont S0, S1,... (jusqu’à n-1).

**Exemples**

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|evenNumbers|even_and_mod3|even_and_mod3_and_mod5|
|---|---|---|
|50|16|3|