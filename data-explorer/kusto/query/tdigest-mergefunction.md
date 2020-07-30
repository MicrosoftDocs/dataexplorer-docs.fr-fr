---
title: tdigest_merge ()-Azure Explorateur de données
description: Cet article décrit tdigest_merge () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: f85c2c45ff4e69ba59f2a13313c8c2ac494c56a6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340987"
---
# <a name="tdigest_merge"></a>tdigest_merge()

Fusionne `tdigest` les résultats (version scalaire de la version de l’agrégat [`tdigest_merge()`](tdigest-merge-aggfunction.md) ).

En savoir plus sur l’algorithme sous-jacent (T-Digest) et l’erreur estimée [ici](percentiles-aggfunction.md#estimation-error-in-percentiles).

## <a name="syntax"></a>Syntaxe

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` -Un alias.

## <a name="arguments"></a>Arguments

* Colonnes dont les `tdigest` valeurs doivent être fusionnées.

## <a name="returns"></a>Retourne

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
