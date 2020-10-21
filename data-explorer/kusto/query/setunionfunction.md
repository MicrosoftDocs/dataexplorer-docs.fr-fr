---
title: set_union ()-Azure Explorateur de données
description: Cet article décrit set_union () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 2fff5763f7eba13e48d0cbdb0e85af666c385308
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242634"
---
# <a name="set_union"></a>set_union()

Retourne un `dynamic` tableau de l’ensemble de toutes les valeurs distinctes qui se trouvent dans l’un des tableaux-(Arr1 ∪ Arr2 ∪...).

## <a name="syntax"></a>Syntaxe

`set_union(`*Arr1* `, ` *Arr2* `[` ,` *arr3*, ...]``)`

## <a name="arguments"></a>Arguments

* *Arr1... arrN*: tableaux d’entrée pour créer un ensemble d’unions (au moins deux tableaux). Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

## <a name="returns"></a>Retours

Retourne un tableau dynamique de l’ensemble de toutes les valeurs distinctes qui sont dans l’un des tableaux. Consultez [`set_intersect()`](setintersectfunction.md)  et [`set_difference()`](setdifferencefunction.md) .

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w)
| project set_union(a1, a2, a3)
```

|Column1|
|---|
|[1, 2, 4, 8]|
|[2, 4, 8, 16]|
|[3, 6, 12, 24]|
