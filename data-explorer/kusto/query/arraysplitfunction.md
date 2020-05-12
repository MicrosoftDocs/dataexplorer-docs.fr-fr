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
ms.openlocfilehash: 102077c9c1116bd9476c6dae59d993a6379b69bd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225548"
---
# <a name="array_split"></a>array_split()

Fractionne un tableau en plusieurs tableaux en fonction des index fractionnés et comprime le tableau généré dans un tableau dynamique.

**Syntaxe**

`array_split`(*`arr`*, *`indices`*)

**Arguments**

* *`arr`*: Tableau d’entrée à fractionner, doit être un tableau dynamique.
* *`indices`*: Entier ou tableau dynamique d’entiers avec les index fractionnés (base zéro), les valeurs négatives sont converties en array_length valeur +.

**Retourne**

Tableau dynamique contenant N + 1 tableaux avec les valeurs comprises dans la plage `[0..i1), [i1..i2), ... [iN..array_length)` `arr` , où N est le nombre d’index d’entrée et `i1...iN` les index.

**Exemples**

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
