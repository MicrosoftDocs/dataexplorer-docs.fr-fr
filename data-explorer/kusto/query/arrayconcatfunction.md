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
ms.openlocfilehash: 681178cc12d145b1c574357e87ae4f7b33d736c4
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225647"
---
# <a name="array_concat"></a>array_concat()

Concatène un certain nombre de tableaux dynamiques en un seul tableau.

**Syntaxe**

`array_concat(`*Arr1* `[` , ` *arr2*, ...]` )»

**Arguments**

* *Arr1... arrN*: tableaux d’entrée à concaténer dans un tableau dynamique. Tous les arguments doivent être des tableaux dynamiques (voir [pack_array](packarrayfunction.md)). 

**Retourne**

Tableau dynamique de tableaux avec Arr1, Arr2,..., arrN.

**Exemple**

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
