---
title: array_slice ()-Azure Explorateur de données
description: Cet article décrit array_slice () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: ecb30d00a6c95e3754686eb264d9439c1c1e605f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246765"
---
# <a name="array_slice"></a>array_slice()

Extrait un segment d’un tableau dynamique.

## <a name="syntax"></a>Syntaxe

`array_slice`(*`arr`*, *`start`*, *`end`*)

## <a name="arguments"></a>Arguments

* *`arr`*: Le tableau d’entrée pour extraire la tranche de doit être un tableau dynamique.
* *`start`*: index de début de base zéro (inclusive) de la tranche, les valeurs négatives sont converties en array_length + Start.
* *`end`*: index de fin (inclusive) de base zéro de la tranche, les valeurs négatives sont converties en array_length + end.

Remarque : les index en dehors des limites sont ignorés.

## <a name="returns"></a>Retours

Tableau dynamique des valeurs de la plage [ `start..end` ] à partir de `arr` .

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1, 2, 3]|[2,3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|découpées|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4, 5]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|découpées|
|---|---|
|[1, 2, 3, 4, 5]|[3,4]|
