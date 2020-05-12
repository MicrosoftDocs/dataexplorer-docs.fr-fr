---
title: dcount_hll ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit dcount_hll () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: d4a76a30526f5fecbafafd735cf72de92aae7644
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225188"
---
# <a name="dcount_hll"></a>dcount_hll()

Calcule le CpteDom à partir des résultats de HLL (qui a été généré par [HLL](hll-aggfunction.md) ou [hll_merge](hll-merge-aggfunction.md)).

En savoir plus sur l' [algorithme sous-jacent (*H*yper*l*og*l*) et la précision de l’estimation](dcount-aggfunction.md#estimation-accuracy).

**Syntaxe**

`dcount_hll(`*Expr*`)`

**Arguments**

* *Expr*: expression qui a été générée par [HLL](hll-aggfunction.md) ou [HLL-Merge](hll-merge-aggfunction.md)

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