---
title: hll_merge ()-Azure Explorateur de données
description: Cet article décrit hll_merge () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 2cddd645b0b89b3356adabae26874f93b41f8815
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226548"
---
# <a name="hll_merge"></a>hll_merge()

Fusionne `hll` les résultats (version scalaire de la version de l’agrégat [`hll_merge()`](hll-merge-aggfunction.md) ).

En savoir plus sur l' [algorithme sous-jacent (*H*yper*l*og*l*) et la précision de l’estimation](dcount-aggfunction.md#estimation-accuracy).

**Syntaxe**

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

**Arguments**

* Colonnes dont les `hll` valeurs doivent être fusionnées.

**Retourne**

Résultat de la fusion des colonnes `*Exrp1*` , `*Expr2*` ,... `*ExprN*` en une seule `hll` valeur.

**Exemples**

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
