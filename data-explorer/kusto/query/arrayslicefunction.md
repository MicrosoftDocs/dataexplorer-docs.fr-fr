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
ms.openlocfilehash: 612829f2cbcd8b3f495d516b254faf7a9cf6919a
ms.sourcegitcommit: 02236d1f23f48f9dd41cc7433f46991356a869fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84306569"
---
# <a name="array_slice"></a>array_slice()

Extrait un segment d’un tableau dynamique.

**Syntaxe**

`array_slice`(*`arr`*, *`start`*, *`end`*)

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
