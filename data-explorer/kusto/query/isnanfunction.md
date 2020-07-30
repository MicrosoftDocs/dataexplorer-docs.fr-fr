---
title: isNaN ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit IsNaN () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5597f21d5e426329e2793978a6b207efc3868d13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347219"
---
# <a name="isnan"></a>isnan()

Retourne une valeur indiquant si l’entrée n’est pas un nombre (NaN).  

## <a name="syntax"></a>Syntaxe

`isnan(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>Retourne

Valeur différente de zéro (true) si x est NaN ; et zéro (false) dans le cas contraire.

**Voir aussi**

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