---
title: set_intersect() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit set_intersect() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 0a1ef86573a408f644e26b3b23f0db42e327573a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507750"
---
# <a name="set_intersect"></a>set_intersect()

Retourne `dynamic` un tableau (JSON) de l’ensemble de toutes les valeurs distinctes qui sont dans tous les tableaux - (arr1 - arr2 - ...).

**Syntaxe**

`set_intersect(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**Arguments**

* *arr1... arrN*: Tableaux d’entrée pour créer un ensemble de croisement (au moins deux tableaux). Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

**Retourne**

Retourne un tableau dynamique de l’ensemble de toutes les valeurs distinctes qui sont dans tous les tableaux. Voir [`set_union()`](setunionfunction.md) [`set_difference()`](setdifferencefunction.md)et .

**Exemple**

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
|[1]|
|[2]|
|[3]|

```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|Arr|
|---|
|[]|