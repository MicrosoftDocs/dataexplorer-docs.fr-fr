---
title: isinf, ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit isinf, () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d84d88c6f3a2b4a2a1ef03ba9eec36f84e3c574
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253263"
---
# <a name="isinf"></a>isinf()

Retourne une valeur indiquant si l’entrée est une valeur infinie (positive ou négative).  

## <a name="syntax"></a>Syntaxe

`isinf(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>Retours

Valeur différente de zéro (true) si x est un entier positif ou négatif ; et zéro (false) dans le cas contraire.

## <a name="see-also"></a>Voir aussi

* Pour vérifier si la valeur est null, consultez [IsNull ()](isnullfunction.md).
* Pour vérifier si la valeur est finie, consultez [isFinite, ()](isfinitefunction.md).
* Pour vérifier si la valeur est NaN (not-a-Number), consultez [IsNaN ()](isnanfunction.md).

## <a name="example"></a>Exemple

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isinf=isinf(div)
```

|x|y|div|isinf|
|---|---|---|---|
|-1|0|-∞|1|
|0|0|NaN|0|
|1|0|∞|1|
