---
title: set_union() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit set_union() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: de9220ea6f9e23e458dd379fb164317842a48c94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507665"
---
# <a name="set_union"></a>set_union()

Retourne `dynamic` un tableau (JSON) de l’ensemble de toutes les valeurs distinctes qui sont dans l’un des tableaux - (arr1 - arr2 - ...).

**Syntaxe**

`set_union(`*arr1*`, `*arr2*`[`,` *arr3*, ...]``)`

**Arguments**

* *arr1... arrN*: Tableaux d’entrée pour créer un ensemble syndical (au moins deux tableaux). Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

**Retourne**

Retourne un tableau dynamique de l’ensemble de toutes les valeurs distinctes qui sont dans n’importe lequel des tableaux. Voir [`set_intersect()`](setintersectfunction.md) [`set_difference()`](setdifferencefunction.md)et .

**Exemple**

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
|[1,2,4,8]|
|[2,4,8,16]|
|[3,6,12,24]|