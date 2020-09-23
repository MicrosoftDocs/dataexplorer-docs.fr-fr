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
ms.openlocfilehash: f73effae8d91524f46548d57288a23d79cffd0a5
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103271"
---
# <a name="isnan"></a>isnan()

Retourne une valeur indiquant si l’entrée n’est pas un nombre (NaN).  

## <a name="syntax"></a>Syntaxe

`isnan(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>retourne :

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