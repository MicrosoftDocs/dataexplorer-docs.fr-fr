---
title: isFinite, ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit isFinite, () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f53c74fd2ac56219351c4d194c93e9fa4b627cf4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347253"
---
# <a name="isfinite"></a>isfinite()

Retourne une valeur indiquant si l’entrée est une valeur finie (n’est ni infinie ni NaN).

## <a name="syntax"></a>Syntaxe

`isfinite(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>Retourne

Valeur différente de zéro (true) si x est fini ; et zéro (false) dans le cas contraire.

**Voir aussi**

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