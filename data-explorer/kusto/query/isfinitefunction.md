---
title: isFinite, ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit isFinite, () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b428d43afd7984bbcf19351da702517a3a1244a9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242315"
---
# <a name="isfinite"></a>isfinite()

Retourne une valeur indiquant si l’entrée est une valeur finie (n’est ni infinie ni NaN).

## <a name="syntax"></a>Syntaxe

`isfinite(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>Retours

Valeur différente de zéro (true) si x est fini ; et zéro (false) dans le cas contraire.

## <a name="see-also"></a>Voir aussi

* Pour vérifier si la valeur est null, consultez [IsNull ()](isnullfunction.md).
* Pour vérifier si la valeur est infinie, consultez [isinf, ()](isinffunction.md).
* Pour vérifier si la valeur est NaN (not-a-Number), consultez [IsNaN ()](isnanfunction.md).

## <a name="example"></a>Exemple

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|isFinite,|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|NaN|0|
|1|0|∞|0|