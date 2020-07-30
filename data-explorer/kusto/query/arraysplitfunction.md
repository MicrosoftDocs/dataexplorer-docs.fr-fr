---
title: array_split ()-Azure Explorateur de données
description: Cet article décrit array_split () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: be993f3b0a58b56b9b4d171378bf71a645e77f1a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349514"
---
# <a name="array_split"></a>array_split()

Fractionne un tableau en plusieurs tableaux en fonction des index fractionnés et comprime le tableau généré dans un tableau dynamique.

## <a name="syntax"></a>Syntaxe

`array_split`(*`arr`*, *`indices`*)

## <a name="arguments"></a>Arguments

* *`arr`*: Tableau d’entrée à fractionner, doit être un tableau dynamique.
* *`indices`*: Entier ou tableau dynamique d’entiers avec les index fractionnés (base zéro), les valeurs négatives sont converties en array_length valeur +.

## <a name="returns"></a>Retourne

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
