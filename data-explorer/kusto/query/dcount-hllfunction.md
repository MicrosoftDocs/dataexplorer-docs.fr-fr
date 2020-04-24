---
title: dcount_hll() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit dcount_hll() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: a0c921efa90f5d66fe42fa6ee872204b5bb399cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516148"
---
# <a name="dcount_hll"></a>dcount_hll()

Calcule le dcount à partir des résultats de hll (qui a été généré par [hll](hll-aggfunction.md) ou [hll_merge](hll-merge-aggfunction.md)).

En savoir plus sur [l’algorithme sous-jacent (*H*yper*L*og*L*og) et la précision d’estimation](dcount-aggfunction.md#estimation-accuracy).

**Syntaxe**

`dcount_hll(`*Expr*`)`

**Arguments**

* *Expr*: Expression générée par [hll](hll-aggfunction.md) ou [hll-merge](hll-merge-aggfunction.md)

**Retourne**

Le nombre distinct de chaque valeur dans *Expr*

**Exemples**

```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|