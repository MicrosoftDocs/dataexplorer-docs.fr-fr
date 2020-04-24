---
title: array_slice() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit array_slice() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: ed5c320ef639f7545e19bcaabd9b53f4424c959d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518579"
---
# <a name="array_slice"></a>array_slice()

Extrait une tranche d’un tableau dynamique.

**Syntaxe**

`array_slice(`*arr*, *commencer*, *fin*]`)`

**Arguments**

* *arr*: Le tableau d’entrée pour extraire la tranche de, doit être un tableau dynamique.
* *début*: indice de démarrage (inclusif) à base de zéro de la tranche, les valeurs négatives sont converties en array_length-start.
* *fin*: indice final à base nulle (inclusive) de la tranche, les valeurs négatives sont converties en array_length.fin.

Remarque : les indices hors limites sont ignorés.

**Retourne**

Gamme dynamique des valeurs de la gamme [start.. fin] de arr.

**Exemples**

1.
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|Arr|Tranché|
|---|---|
|[1,2,3]|[2,3]|


2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|Arr|Tranché|
|---|---|
|[1,2,3,4,5]|[3,4,5]|


3.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|Arr|Tranché|
|---|---|
|[1,2,3,4,5]|[3,4]|