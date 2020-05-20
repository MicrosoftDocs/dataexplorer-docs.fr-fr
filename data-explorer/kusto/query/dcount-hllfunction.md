---
title: dcount_hll ()-Azure Explorateur de données
description: Cet article décrit dcount_hll () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 1b1b0c2313f32044a7988e0992c00786885ce2aa
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550297"
---
# <a name="dcount_hll"></a>dcount_hll()

Calcule le CpteDom à partir des résultats de HLL (générés par [HLL](hll-aggfunction.md) ou [hll_merge](hll-merge-aggfunction.md)).

En savoir plus sur l' [algorithme sous-jacent (*H*yper*l*og*l*) et la précision de l’estimation](dcount-aggfunction.md#estimation-accuracy).

**Syntaxe**

`dcount_hll(`*Expr*`)`

**Arguments**

* *Expr*: expression générée par [HLL](hll-aggfunction.md) ou [HLL-Merge](hll-merge-aggfunction.md)

**Retourne**

Compte distinct de chaque valeur dans *expr*

**Exemples**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|
