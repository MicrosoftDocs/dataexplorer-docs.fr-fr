---
title: dcount_hll ()-Azure Explorateur de données
description: Cet article décrit dcount_hll () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 743f35b6bf6d461c1d08c3082bb235b88b57ada2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252376"
---
# <a name="dcount_hll"></a>dcount_hll()

Calcule le CpteDom à partir des résultats de HLL (générés par [HLL](hll-aggfunction.md) ou [hll_merge](hll-merge-aggfunction.md)).

En savoir plus sur l' [algorithme sous-jacent (*H*yper*l*og*l*) et la précision de l’estimation](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntaxe

`dcount_hll(`*Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression générée par [HLL](hll-aggfunction.md) ou [HLL-Merge](hll-merge-aggfunction.md)

## <a name="returns"></a>Retours

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
