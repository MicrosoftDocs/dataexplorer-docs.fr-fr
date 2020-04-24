---
title: set_difference() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit set_difference() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: d4edb8ec46fca99b7dd58b11bbd54442a9340c7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507801"
---
# <a name="set_difference"></a>set_difference()

Retourne `dynamic` un tableau (JSON) de l’ensemble de toutes les valeurs distinctes qui sont dans le premier tableau, mais ne sont pas dans d’autres tableaux - (((arr1 - arr2) - arr3) - ...).

**Syntaxe**

`set_difference(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**Arguments**

* *arr1... arrN*: Tableaux d’entrée pour créer un ensemble de différence (au moins deux tableaux). Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

**Retourne**

Retourne un tableau dynamique de l’ensemble de toutes les valeurs distinctes qui sont dans arr1 mais ne sont pas dans d’autres tableaux. Voir [`set_union()`](setunionfunction.md) [`set_intersect()`](setintersectfunction.md)et .

**Exemple**

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
|[8]|
|[12]|

```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|Arr|
|---|
|[]|