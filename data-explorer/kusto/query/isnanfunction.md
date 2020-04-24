---
title: isnan() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit isnan () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 123d9cd32d645bb1225983138973a17b6bb9ecf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513564"
---
# <a name="isnan"></a>isnan()

Retourne si l’entrée est not-a-Number (NaN) valeur.  

**Syntaxe**

`isnan(`*X*`)`

**Arguments**

* *x*: Un vrai nombre.

**Retourne**

Une valeur non nulle (vraie) si x est NaN; et zéro (faux) autrement.

**Voir aussi**

* Pour vérifier si la valeur est nulle, voir [isnull()](isnullfunction.md).
* Pour vérifier si la valeur est finie, voir [isfinite()](isfinitefunction.md).
* Pour vérifier si la valeur est infinie, voir [isinf()](isinffunction.md).

**Exemple**

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|isnan (isnan)|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|