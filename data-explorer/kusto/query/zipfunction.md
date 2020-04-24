---
title: zip() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit zip () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd407ce652d41471be5b30a15c2c09b9f608edb1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504231"
---
# <a name="zip"></a>zip()

La `zip` fonction accepte n’importe quel nombre de `dynamic` tableaux, et renvoie un tableau dont les éléments sont chacun un tableau contenant les éléments des tableaux d’entrée du même index.

**Syntaxe**

`zip(`*tableau1* `,` *array2*`, ... )`

**Arguments**

Entre 2 et 16 tableaux dynamiques.

**Exemples**

L’exemple suivant retourne `[[1,2],[3,4],[5,6]]`:

```kusto
print zip(dynamic([1,3,5]), dynamic([2,4,6]))
```

L’exemple suivant retourne `[["A",{}], [1,"B"], [1.5, null]]`:

```kusto
print zip(dynamic(["A", 1, 1.5]), dynamic([{}, "B"]))
```

L’exemple suivant retourne `[[1,"one"],[2,"two"],[3,"three"]]`:

```kusto
datatable(a:int, b:string) [1,"one",2,"two",3,"three"]
| summarize a = make_list(a), b = make_list(b)
| project zip(a, b)
```