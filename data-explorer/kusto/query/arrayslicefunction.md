---
title: array_slice ()-Azure Explorateur de données
description: Cet article décrit array_slice () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: 98ed69475232dde474f21d1f4a7f792c82a69637
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225613"
---
# <a name="array_slice"></a>array_slice()

Extrait un segment d’un tableau dynamique.

**Syntaxe**

`array_slice`(*`arr`*, *`start`*, *`end`*])

**Arguments**

* *`arr`*: Le tableau d’entrée pour extraire la tranche de doit être un tableau dynamique.
* *`start`*: index de début de base zéro (inclusive) de la tranche, les valeurs négatives sont converties en array_length + Start.
* *`end`*: index de fin (inclusive) de base zéro de la tranche, les valeurs négatives sont converties en array_length + end.

Remarque : les index en dehors des limites sont ignorés.

**Retourne**

Tableau dynamique des valeurs de la plage [ `start..end` ] à partir de `arr` .

**Exemples**

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
