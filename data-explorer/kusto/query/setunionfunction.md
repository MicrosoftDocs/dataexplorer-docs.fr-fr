---
title: set_union ()-Azure Explorateur de données
description: Cet article décrit set_union () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 19c7b47318d0748510aba146968149cd0eb246a2
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264570"
---
# <a name="set_union"></a>set_union()

Retourne un `dynamic` tableau de l’ensemble de toutes les valeurs distinctes qui se trouvent dans l’un des tableaux-(Arr1 ∪ Arr2 ∪...).

**Syntaxe**

`set_union(`*Arr1* `, ` *Arr2* `[` ,` *arr3*, ...]``)`

**Arguments**

* *Arr1... arrN*: tableaux d’entrée pour créer un ensemble d’unions (au moins deux tableaux). Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

**Retourne**

Retourne un tableau dynamique de l’ensemble de toutes les valeurs distinctes qui sont dans l’un des tableaux. Consultez [`set_intersect()`](setintersectfunction.md) et [`set_difference()`](setdifferencefunction.md) .

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w)
| project set_union(a1, a2, a3)
```

|Colonne1|
|---|
|[1, 2, 4, 8]|
|[2, 4, 8, 16]|
|[3, 6, 12, 24]|
