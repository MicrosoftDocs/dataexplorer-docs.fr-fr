---
title: array_concat ()-Azure Explorateur de données
description: Cet article décrit array_concat () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ecaca4aea221ca2b880b798757de64787901a0cb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349599"
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

|Colonne1|
|---|
|[1, 2, 4, 1, 2]|
|[2, 4, 8, 2,4]|
|[3, 6, 12, 3, 6]|
