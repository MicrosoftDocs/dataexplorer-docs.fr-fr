---
title: set_intersect ()-Azure Explorateur de données
description: Cet article décrit set_intersect () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 682cff2dc5a4334a4543767048429b1ea04dc329
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242436"
---
# <a name="set_intersect"></a>set_intersect()

Retourne un `dynamic` tableau de l’ensemble de toutes les valeurs distinctes qui se trouvent dans tous les tableaux (Arr1 ∩ Arr2 ∩...).

## <a name="syntax"></a>Syntaxe

`set_intersect(`*Arr1* `, ` *Arr2* `[` ,` *arr3*, ...])`

## <a name="arguments"></a>Arguments

* *Arr1... arrN*: tableaux d’entrée pour créer un ensemble d’intersection (au moins deux tableaux). Tous les arguments doivent être des tableaux dynamiques. Pour plus d’informations, consultez [pack_array](packarrayfunction.md). 

## <a name="returns"></a>Retours

Retourne un tableau dynamique du jeu de toutes les valeurs distinctes qui se trouvent dans tous les tableaux. Consultez [`set_union()`](setunionfunction.md) et [`set_difference()`](setdifferencefunction.md) .

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w,x)
| project set_intersect(a1, a2, a3)
```

|Column1|
|---|
| [1]|
|2|
|1,3|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|arr|
|---|
|[]|
