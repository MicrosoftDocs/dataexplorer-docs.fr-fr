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
ms.openlocfilehash: 7e13a9b652e1bdadb325cd866bddd78761b25b85
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372392"
---
# <a name="set_difference"></a>set_difference()

Retourne un `dynamic` tableau (JSON) de l’ensemble de toutes les valeurs distinctes qui se trouvent dans le premier tableau, mais qui ne sont pas dans d’autres tableaux-(((Arr1 \ Arr2) \ arr3) \...).

**Syntaxe**

`set_difference(`*Arr1* `, ` *Arr2* `[` ,` *arr3*, ...])`

**Arguments**

* *Arr1... arrN*: tableaux d’entrée pour créer un ensemble de différences (au moins deux tableaux). Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

**Retourne**

Retourne un tableau dynamique de l’ensemble de toutes les valeurs distinctes qui se trouvent dans Arr1, mais qui ne sont pas dans d’autres tableaux. Consultez [`set_union()`](setunionfunction.md) et [`set_intersect()`](setintersectfunction.md) .

**Exemple**

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
|douze|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|arr|
|---|
|[]|