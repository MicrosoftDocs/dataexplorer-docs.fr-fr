---
title: hll_merge ()-Azure Explorateur de données
description: Cet article décrit hll_merge () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 6bf364106bef8fbe626f96c22502dae748884180
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252969"
---
# <a name="hll_merge"></a>hll_merge()

Fusionne `hll` les résultats (version scalaire de la version de l’agrégat [`hll_merge()`](hll-merge-aggfunction.md) ).

En savoir plus sur l' [algorithme sous-jacent (*H*yper*l*og*l*) et la précision de l’estimation](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntaxe

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

## <a name="arguments"></a>Arguments

* Colonnes dont les `hll` valeurs doivent être fusionnées.

## <a name="returns"></a>Retours

Résultat de la fusion des colonnes `*Exrp1*` , `*Expr2*` ,... `*ExprN*` en une seule `hll` valeur.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|`dcount_hll_merged`|
|---|
|20|
