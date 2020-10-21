---
title: zip ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit zip () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33b914babb8c197f997326cd6dd44654c05d1d9d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253140"
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