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
ms.openlocfilehash: 35dab83a658b714a220c7fbd6ff751627c0741e4
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741785"
---
# <a name="hll_merge"></a>hll_merge()

Fusionne `hll` les résultats (version scalaire de la version [`hll_merge()`](hll-merge-aggfunction.md)de l’agrégat).

En savoir plus sur l' [algorithme sous-jacent (*H*yper*l*og*l*) et la précision de l’estimation](dcount-aggfunction.md#estimation-accuracy).

**Syntaxe**

`hll_merge(`*Expr1* `,` *expr2*`, ...)`

**Arguments**

* Colonnes dont les `hll` valeurs doivent être fusionnées.

**Retourne**

Résultat de la fusion des colonnes `*Exrp1*`, `*Expr2*`,... `*ExprN*` à une `hll` valeur.

**Exemples**

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