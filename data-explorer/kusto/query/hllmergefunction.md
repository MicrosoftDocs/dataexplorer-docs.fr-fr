---
title: hll_merge() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit hll_merge() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 10e726af4e9dd2e129b526f016a7c37dc0c99d50
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514091"
---
# <a name="hll_merge"></a>hll_merge()

Fusionne les résultats de hll (version [`hll_merge()`](hll-merge-aggfunction.md)scalaire de la version globale ).

En savoir plus sur [l’algorithme sous-jacent (*H*yper*L*og*L*og) et la précision d’estimation](dcount-aggfunction.md#estimation-accuracy).

**Syntaxe**

`hll_merge(`*Expr1* `,` *Expr2 Expr2*`, ...)`

**Arguments**

* Colonnes qui a les valeurs de hll à fusionner.

**Retourne**

Le résultat pour la `*Exrp1*` `*Expr2*`fusion des colonnes , , ... `*ExprN*` à une valeur de hll.

**Exemples**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|dcount_hll_merged|
|---|
|20|