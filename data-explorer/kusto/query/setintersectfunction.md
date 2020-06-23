---
title: set_intersect ()-Azure Explorateur de données
description: Cet article décrit set_intersect () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: f611f660a791117159ad5fce4c024914d9e6909b
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264740"
---
# <a name="set_intersect"></a>set_intersect()

Retourne un `dynamic` tableau de l’ensemble de toutes les valeurs distinctes qui se trouvent dans tous les tableaux (Arr1 ∩ Arr2 ∩...).

**Syntaxe**

`set_intersect(`*Arr1* `, ` *Arr2* `[` ,` *arr3*, ...])`

**Arguments**

* *Arr1... arrN*: tableaux d’entrée pour créer un ensemble d’intersection (au moins deux tableaux). Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

**Retourne**

Retourne un tableau dynamique du jeu de toutes les valeurs distinctes qui se trouvent dans tous les tableaux. Consultez [`set_union()`](setunionfunction.md) et [`set_difference()`](setdifferencefunction.md) .

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w,x)
| project set_intersect(a1, a2, a3)
```

|Colonne1|
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
