---
title: array_split ()-Azure Explorateur de données
description: Cet article décrit array_split () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: b1dad77bd58d64bfcd167cc7d30f0986a54f13c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252793"
---
# <a name="array_split"></a>array_split()

Fractionne un tableau en plusieurs tableaux en fonction des index fractionnés et comprime le tableau généré dans un tableau dynamique.

## <a name="syntax"></a>Syntaxe

`array_split`(*`arr`*, *`indices`*)

## <a name="arguments"></a>Arguments

* *`arr`*: Tableau d’entrée à fractionner, doit être un tableau dynamique.
* *`indices`*: Entier ou tableau dynamique d’entiers avec les index fractionnés (base zéro), les valeurs négatives sont converties en array_length valeur +.

## <a name="returns"></a>Retours

Tableau dynamique contenant N + 1 tableaux avec les valeurs comprises dans la plage `[0..i1), [i1..i2), ... [iN..array_length)` `arr` , où N est le nombre d’index d’entrée et `i1...iN` les index.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```

|`arr`|`arr_split`|
|---|---|
|[1, 2, 3, 4, 5]|[[1, 2], [3, 4, 5]]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```

|`arr`|`arr_split`|
|---|---|
|[1, 2, 3, 4, 5]|[[1], [2, 3], [4, 5]]|
