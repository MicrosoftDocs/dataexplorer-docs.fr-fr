---
title: isfinite () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit isfinite () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a17a39cce91fe039b2cf55cc5c98dba111cc334
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513598"
---
# <a name="isfinite"></a>isfinite()

Retourne si l’entrée est une valeur finie (n’est ni infinie ni NaN).

**Syntaxe**

`isfinite(`*X*`)`

**Arguments**

* *x*: Un vrai nombre.

**Retourne**

Une valeur non nulle (vraie) si x est finie; et zéro (faux) autrement.

**Voir aussi**

* Pour vérifier si la valeur est nulle, voir [isnull()](isnullfunction.md).
* Pour vérifier si la valeur est infinie, voir [isinf()](isinffunction.md).
* Pour vérifier si la valeur est NaN (Not-a-Number), voir [isnan()](isnanfunction.md).

**Exemple**

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|isfinite|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|NaN|0|
|1|0|∞|0|