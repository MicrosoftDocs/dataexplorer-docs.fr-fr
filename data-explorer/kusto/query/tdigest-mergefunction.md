---
title: tdigest_merge() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit tdigest_merge() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 988d7f05791723a823a5850f6865a780477f7bd4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506373"
---
# <a name="tdigest_merge"></a>tdigest_merge()

Fusionne les résultats les plus tdigestes [`tdigest_merge()`](tdigest-merge-aggfunction.md)(version scalaire de la version globale).

En savoir plus sur l’algorithme sous-jacent (T-Digest) et l’erreur estimée [ici](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Syntaxe**

`merge_tdigests(`*Expr1* `,` *Expr2 Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` - Un alias.

**Arguments**

* Colonnes qui a les tdigestes à fusionner.

**Retourne**

Le résultat pour la `*Expr1*` `*Expr2*`fusion des colonnes , , ... `*ExprN*` à un tdigest.

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