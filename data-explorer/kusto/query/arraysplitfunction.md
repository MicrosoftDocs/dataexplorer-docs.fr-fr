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
ms.openlocfilehash: 360a958a08b93d22dabd15b187f8227606486709
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737383"
---
# <a name="array_split"></a>array_split()

Fractionne un tableau en plusieurs tableaux en fonction des index fractionnés et comprime le tableau généré dans un tableau dynamique.

**Syntaxe**

`array_split`(*`arr`*, *`indices`*)

**Arguments**

* *`arr`*: Tableau d’entrée à fractionner, doit être un tableau dynamique.
* *`indices`*: Entier ou tableau dynamique d’entiers avec les index fractionnés (base zéro), les valeurs négatives sont converties en array_length valeur +.

**Retourne**

Tableau dynamique contenant N + 1 tableaux avec les valeurs comprises dans `[0..i1), [i1..i2), ... [iN..array_length)` la `arr`plage, où N est le nombre d’index d’entrée et `i1...iN` les index.

**Exemples**

```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```

|`arr`|`arr_split`|
|---|---|
|[1, 2, 3, 4, 5]|[[1, 2], [3, 4, 5]]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```

|`arr`|`arr_split`|
|---|---|
|[1, 2, 3, 4, 5]|[[1], [2, 3], [4, 5]]|
