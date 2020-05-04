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
ms.openlocfilehash: f2de8d91b73a3495c8b0902d710bd87c7fecbafa
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737553"
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

Tableau dynamique des valeurs de la plage [`start..end`] à partir `arr`de.

**Exemples**


```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1, 2, 3]|[2,3]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|découpées|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4, 5]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|découpées|
|---|---|
|[1, 2, 3, 4, 5]|[3,4]|