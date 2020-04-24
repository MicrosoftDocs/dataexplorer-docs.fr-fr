---
title: array_split() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit array_split() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 4d5d8ce5e918f335f1f26f4e3fc045ab0fa6e3a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518562"
---
# <a name="array_split"></a>array_split()

Divise un tableau à plusieurs tableaux en fonction des indices fractionnés et emballe le tableau généré dans un tableau dynamique.

**Syntaxe**

`array_split(`*arr*, *indices*`)`

**Arguments**

* *arr*: Le tableau d’entrée à diviser, doit être un tableau dynamique.
* *indices*: Integer ou gamme dynamique d’intégrants avec les indices fractionnés (zéro à base), les valeurs négatives sont converties en array_length valeur.

**Retourne**

Tableau dynamique contenant des tableaux N-1 avec les valeurs de la gamme [0..i1), [i1.. i2), ... [iN.. array_length) de arr, où N est le nombre d’indices d’entrée et i1. iN sont les indices.

**Exemples**

1.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```
|Arr|arr_split|
|---|---|
|[1,2,3,4,5]|[[1,2],[3,4,5]]|



2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```
|Arr|arr_split|
|---|---|
|[1,2,3,4,5]|[[1],[2,3],[4,5]]|