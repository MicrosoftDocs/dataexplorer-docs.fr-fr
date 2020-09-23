---
title: isinf, ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit isinf, () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e9fa24aeec2cc70b41e1fcd4f9d1185ef80b714b
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103234"
---
# <a name="isinf"></a>isinf()

Retourne une valeur indiquant si l’entrée est une valeur infinie (positive ou négative).  

## <a name="syntax"></a>Syntaxe

`isinf(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>retourne :

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
