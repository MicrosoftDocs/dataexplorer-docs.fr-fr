---
title: tdigest () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit tdigest () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 9e8f61038856096d4ca6676c5304bb9fc6112070
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250707"
---
# <a name="tdigest-aggregation-function"></a>tdigest () (fonction d’agrégation)

Calcule les résultats intermédiaires de [`percentiles()`](percentiles-aggfunction.md) dans le groupe.

> [!NOTE]
> Peut uniquement être utilisé dans le contexte de l’agrégation, à l’intérieur d’une [synthèse](summarizeoperator.md).

Pour plus d’informations, consultez l' [algorithme sous-jacent (T-Digest) et l’erreur estimée](percentiles-aggfunction.md#estimation-error-in-percentiles).

## <a name="syntax"></a>Syntaxe

`summarize` `tdigest`(*`Expr`* [`,` *`WeightExpr`*])

## <a name="arguments"></a>Arguments

* *Expr*: expression utilisée pour le calcul de l’agrégation.
* *WeightExpr*: expression utilisée comme poids des valeurs pour le calcul d’agrégation.

    
## <a name="returns"></a>Retours

Résultats intermédiaires des centiles pondérés de `*Expr*` l’ensemble du groupe.
 
 
**Conseils**

* Utilisez la fonction d’agrégation [tdigest_merge ()](tdigest-merge-aggfunction.md) pour fusionner à nouveau la sortie de `tdigest` dans un autre groupe.

* Utilisez la fonction [percentile_tdigest ()](percentile-tdigestfunction.md) pour calculer le centile/percentilew des `tdigest` résultats.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigest(DamageProperty) by State
```

|State|tdigest_DamageProperty|
|---|---|
|SUD DE L’ATLANTIQUE|[[5], [0], [193]]|
|Floride|[[5], [250, 10, 600000, 5000, 375000, 15000000, 20000, 6000000, 0, 110000, 150000, 500, 12000, 30000, 15000, 46000000, 7000000, 6200000, 200000, 40000, 8000, 52000000, 62000000, 1200000, 130000, 1500000, 4000000, 7000, 250000, 875000, 3000, 100000, 10600000, 300000, 1000000, 25000, 75000, 2000, 60000, 10000, 170000, 350000, 50000, 1000, 16000, 80 500, 400000], [9, 1, 1, 22, 1, 1, 9, 1842, 1, 3, 7, 2, 4, 7, 1, 1, 1, 2, 5, 3, 3, 1, 1, 1, 1, 2, 2, 1, 1, 9, 7, 1, 1, 2, 5, 2, 9, 2, 27, 1, 1, 7, 27, 1, 1, 1, 1]]|
|Géorgie|[[5], [468, 209, 300000, 3000, 250000, 775000, 14000, 500000, 0, 75000, 4500000, 500, 6928, 22767, 9714, 800000, 700000, 600000, 150000, 25000, 5000, 1600000, 1250000, 2700000, 1500000, 2250000, 400000, 4000, 175000, 325000, 2500, 73750, 750000, 1400000, 350000, 28000000, 39000, 1500, 35000, 6455, 140000, 225000, 30000, 1000, 110000000, 21700000, 2000, 275000, 200000, 100000, 1000000, 2600000, 370000, 2100000, 355000, 117500, 50000, 20100, 10000], [11, 11, 4, 53, 21, 1, 6, 10, 1317, 8, 1, 56, 8, 6, 7, 1, 1, 1, 1, 29, 69, 1, 2, 1, 1, 1, 3, 14, 5, 1, 3, 4, 4, 1, 4, 1, 5, 14, 3, 5, 2, 1, 9, 96, 1, 1, 72, 1, 10, 17, 3, 1, 1, 1, 1, 2, 21, 4, 31]]|
|MISSISSIPPI|[[5], [267, 55, 90000, 3000, 75000, 300000, 11167, 160000, 0, 32000, 40000, 1000, 7000, 13000, 8000, 400000, 200000, 180000, 50000, 15000, 5000, 700000, 500000, 120000, 650000, 1000000, 150000, 4000, 60000, 100000, 2500, 30000, 250000, 600000, 110000, 12000, 20000, 1500, 17000, 6000, 45000, 70000, 15250, 1219, 10000, 25000, 2000, 80, 65000, 35000, 450000, 1200000, 130000, 750000], [3, 2, 6, 21, 1, 4, 6, 1741, 4, 13, 44, 8, 2, 8, 1, 5, 1, 23, 21, 32, 1, 3, 1, 1, 1, 5, 18, 17, 4, 1, 14, 2, 4, 4, 16, 13, 10, 4, 9, 2, 10, 4, 8, 31, 17, 51, 13, 1, 1, 1, 2, 1, 1]]|
|SAMOA AMÉRICAINES|[[5], [0, 250000], [15, 1]]|
