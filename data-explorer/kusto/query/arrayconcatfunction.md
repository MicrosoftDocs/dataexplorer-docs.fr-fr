---
title: array_concat() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit array_concat () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c66c17ab147eb3d6c5f749e7f28fad347a50ce22
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518749"
---
# <a name="array_concat"></a>array_concat()

Concatenates un certain nombre de tableaux dynamiques à un seul tableau.

**Syntaxe**

`array_concat(`*arr1*`[`` *arr2*, ...]`, )'

**Arguments**

* *arr1... arrN*: Les tableaux d’entrée à concatenated dans un tableau dynamique. Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

**Retourne**

Gamme dynamique de tableaux avec arr1, arr2, ... , arrN.

**Exemple**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|Colonne1|
|---|
|[1,2,4,1,2]|
|[2,4,8,2,4]|
|[3,6,12,3,6]|