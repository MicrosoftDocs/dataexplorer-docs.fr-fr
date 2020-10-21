---
title: array_concat ()-Azure Explorateur de données
description: Cet article décrit array_concat () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4c8e2da4d2ba4ed205987b5a1d063ac2e75ed289
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246913"
---
# <a name="array_concat"></a>array_concat()

Concatène un certain nombre de tableaux dynamiques en un seul tableau.

## <a name="syntax"></a>Syntaxe

`array_concat(`*Arr1* `[` , ` *arr2*, ...]` )»

## <a name="arguments"></a>Arguments

* *Arr1... arrN*: tableaux d’entrée à concaténer dans un tableau dynamique. Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

## <a name="returns"></a>Retours

Tableau dynamique de tableaux avec Arr1, Arr2,..., arrN.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|Column1|
|---|
|[1, 2, 4, 1, 2]|
|[2, 4, 8, 2,4]|
|[3, 6, 12, 3, 6]|
