---
title: tdigest_merge ()-Azure Explorateur de données
description: Cet article décrit tdigest_merge () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 4b7c143d29b8a2f446f4929098e9fac4e6166796
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250614"
---
# <a name="tdigest_merge"></a>tdigest_merge()

Fusionne `tdigest` les résultats (version scalaire de la version de l’agrégat [`tdigest_merge()`](tdigest-merge-aggfunction.md) ).

En savoir plus sur l’algorithme sous-jacent (T-Digest) et l’erreur estimée [ici](percentiles-aggfunction.md#estimation-error-in-percentiles).

## <a name="syntax"></a>Syntaxe

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` -Un alias.

## <a name="arguments"></a>Arguments

* Colonnes dont les `tdigest` valeurs doivent être fusionnées.

## <a name="returns"></a>Retours

Résultat de la fusion des colonnes `*Expr1*` , `*Expr2*` ,... `*ExprN*` à un `tdigest` .

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize tdigestX = tdigest(x), tdigestY = tdigest(y)
| project merged = tdigest_merge(tdigestX, tdigestY)
| project percentile_tdigest(merged, 100, typeof(long))
```

|percentile_tdigest_merged|
|---|
|20|
