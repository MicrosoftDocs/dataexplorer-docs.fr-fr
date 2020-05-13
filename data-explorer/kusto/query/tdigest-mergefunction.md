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
ms.openlocfilehash: 1281e2afdf9770975c6f6f74399f9815adaec045
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83371063"
---
# <a name="tdigest_merge"></a>tdigest_merge()

Fusionne `tdigest` les résultats (version scalaire de la version de l’agrégat [`tdigest_merge()`](tdigest-merge-aggfunction.md) ).

En savoir plus sur l’algorithme sous-jacent (T-Digest) et l’erreur estimée [ici](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Syntaxe**

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` -Un alias.

**Arguments**

* Colonnes dont les `tdigest` valeurs doivent être fusionnées.

**Retourne**

Résultat de la fusion des colonnes `*Expr1*` , `*Expr2*` ,... `*ExprN*` à un `tdigest` .

**Exemples**

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
