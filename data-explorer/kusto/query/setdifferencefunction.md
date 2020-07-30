---
title: set_difference ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit set_difference () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: bd442f70b3411b61f213098fefec918622f8d916
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351180"
---
# <a name="set_difference"></a>set_difference()

Retourne un `dynamic` tableau (JSON) de l’ensemble de toutes les valeurs distinctes qui se trouvent dans le premier tableau, mais qui ne sont pas dans d’autres tableaux-(((Arr1 \ Arr2) \ arr3) \...).

## <a name="syntax"></a>Syntaxe

`set_difference(`*Arr1* `, ` *Arr2* `[` ,` *arr3*, ...])`

## <a name="arguments"></a>Arguments

* *Arr1... arrN*: tableaux d’entrée pour créer un ensemble de différences (au moins deux tableaux). Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

## <a name="returns"></a>Retourne

Retourne un tableau dynamique de l’ensemble de toutes les valeurs distinctes qui se trouvent dans Arr1, mais qui ne sont pas dans d’autres tableaux. Consultez [`set_union()`](setunionfunction.md) et [`set_intersect()`](setintersectfunction.md) .

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(x,y,w)
| project set_difference(a1, a2, a3)
```

|Colonne1|
|---|
|[4]|
|version8|
| [12]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|arr|
|---|
|[]|