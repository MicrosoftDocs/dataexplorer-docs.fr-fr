---
title: isNaN ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit IsNaN () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a8cde58db626ca7bc3433e8e36c8d369a7e592b1
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253244"
---
# <a name="isnan"></a>isnan()

Retourne une valeur indiquant si l’entrée n’est pas un nombre (NaN).  

## <a name="syntax"></a>Syntaxe

`isnan(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>Retours

Valeur différente de zéro (true) si x est NaN ; et zéro (false) dans le cas contraire.

## <a name="see-also"></a>Voir aussi

* Pour vérifier si la valeur est null, consultez [IsNull ()](isnullfunction.md).
* Pour vérifier si la valeur est finie, consultez [isFinite, ()](isfinitefunction.md).
* Pour vérifier si la valeur est infinie, consultez [isinf, ()](isinffunction.md).

## <a name="example"></a>Exemple

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|isNaN|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|