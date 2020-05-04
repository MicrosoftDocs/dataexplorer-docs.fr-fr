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
ms.openlocfilehash: 92dce1a98cc0e24dcfbfcd7cb875fa370e3ae1d0
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737723"
---
# <a name="tdigest_merge"></a>tdigest_merge()

Fusionne `tdigest` les résultats (version scalaire de la version [`tdigest_merge()`](tdigest-merge-aggfunction.md)de l’agrégat).

En savoir plus sur l’algorithme sous-jacent (T-Digest) et l’erreur estimée [ici](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Syntaxe**

`merge_tdigests(`*Expr1* `,` *expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* expr2`, ...)` -un alias.

**Arguments**

* Colonnes dont les `tdigest` valeurs doivent être fusionnées.

**Retourne**

Résultat de la fusion des colonnes `*Expr1*`, `*Expr2*`,... `*ExprN*` à un `tdigest`.

**Exemples**

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