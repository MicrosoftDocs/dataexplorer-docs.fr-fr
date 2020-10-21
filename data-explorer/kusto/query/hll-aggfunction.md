---
title: HLL () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit HLL () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: 4474299c804e1b54d3060d639d171652e770d989
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241617"
---
# <a name="hll-aggregation-function"></a>HLL () (fonction d’agrégation)

Calcule les résultats intermédiaires de dans [`dcount`](dcount-aggfunction.md) le groupe, uniquement dans le contexte d’une agrégation dans un [Résumé](summarizeoperator.md).

En savoir plus sur l' [algorithme sous-jacent (*H*yper*l*og*l*og) et la précision de l’estimation](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntaxe

`summarize hll(`*`Expr`* `[,` *`Accuracy`*`])`

## <a name="arguments"></a>Arguments

* *`Expr`*: Expression qui sera utilisée pour le calcul de l’agrégation. 
* *`Accuracy`*, s’il est spécifié, contrôle l’équilibre entre la vitesse et la précision.

  |Valeur de précision |Précision  |Vitesse  |Error  |
  |---------|---------|---------|---------|
  |`0` | lowest | capacités | 1,6% |
  |`1` | default  | équilibrée | 0,8% |
  |`2` | high | slow | 0,4%  |
  |`3` | high | slow | 0,28% |
  |`4` | très élevé | les plus lents | 0,2% |
    
## <a name="returns"></a>Retours

Résultats intermédiaires du décompte distinct de *`Expr`* l’ensemble du groupe.
 
**Conseils**

1. Vous pouvez utiliser la fonction [`hll_merge`](hll-merge-aggfunction.md) d’agrégation pour fusionner plusieurs `hll` résultats intermédiaires (cela fonctionne uniquement sur la `hll` sortie).

1. Vous pouvez utiliser la fonction [`dcount_hll`](dcount-hllfunction.md) , qui calcule les `dcount` `hll`  /  `hll_merge` fonctions d’agrégation.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|`hll_DamageProperty`|
|---|---|
|2007-09-18 20:00:00.0000000|[[1024, 14], [-5473486921211236216,-6230876016761372746, 3953448761157777955, 4246796580750024372], []]|
|2007-09-20 21:50:00.0000000|[[1024, 14], [4835649640695509390], []]|
|2007-09-29 08:10:00.0000000|[[1024, 14], [4246796580750024372], []]|
|2007-12-30 16:00:00.0000000|[[1024, 14], [4246796580750024372,-8936707700542868125], []]|
