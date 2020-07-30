---
title: zip ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit zip () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 28fc477d4dfc5432434261e493f36985514ea28b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350551"
---
# <a name="zip"></a>zip()

La `zip` fonction accepte un nombre quelconque de `dynamic` tableaux et retourne un tableau dont les éléments sont chacun un tableau contenant les éléments des tableaux d’entrée du même index.

## <a name="syntax"></a>Syntaxe

`zip(`*matrice1* `,` *matrice2*`, ... )`

## <a name="arguments"></a>Arguments

Entre 2 et 16 tableaux dynamiques.

## <a name="examples"></a>Exemples

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