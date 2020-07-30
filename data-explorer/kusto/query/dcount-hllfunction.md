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
ms.openlocfilehash: 2e3847f0ad6c120f076461c5b4774f60349d6125
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348426"
---
# <a name="dcount_hll"></a>dcount_hll()

Calcule le CpteDom à partir des résultats de HLL (générés par [HLL](hll-aggfunction.md) ou [hll_merge](hll-merge-aggfunction.md)).

En savoir plus sur l' [algorithme sous-jacent (*H*yper*l*og*l*) et la précision de l’estimation](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntaxe

`dcount_hll(`*Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression générée par [HLL](hll-aggfunction.md) ou [HLL-Merge](hll-merge-aggfunction.md)

## <a name="returns"></a>Retourne

Compte distinct de chaque valeur dans *expr*

## <a name="examples"></a>Exemples

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
