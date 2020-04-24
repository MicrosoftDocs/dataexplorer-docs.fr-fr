---
title: isinf() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit isinf() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d93697890ee05cabf9ca1830ac047d90d8c9e844
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513581"
---
# <a name="isinf"></a>isinf()

Rendement si l’entrée est une valeur infinie (positive ou négative).  

**Syntaxe**

`isinf(`*X*`)`

**Arguments**

* *x*: Un vrai nombre.

**Retourne**

Une valeur non nulle (vraie) si x est un infini positif ou négatif; et zéro (faux) autrement.

**Voir aussi**

* Pour vérifier si la valeur est nulle, voir [isnull()](isnullfunction.md).
* Pour vérifier si la valeur est finie, voir [isfinite()](isfinitefunction.md).
* Pour vérifier si la valeur est NaN (Not-a-Number), voir [isnan()](isnanfunction.md).

**Exemple**

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
