---
title: beta_cdf() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit beta_cdf() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4faaeddc647d047755108f3c993db855a56de363
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517916"
---
# <a name="beta_cdf"></a>beta_cdf()

Retourne la fonction de distribution bêta cumulative standard.

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

Si *la probabilité* = `beta_cdf(`*x*,... `)`, `beta_inv(`puis *la probabilité*,... `)` *x*x  = .

La distribution Bêta est couramment utilisée pour étudier la variation du pourcentage d’une valeur dans des échantillons. Il peut s’agir, par exemple, de la fraction d’une journée que des gens passent à regarder la télévision.

**Syntaxe**

`beta_cdf(`*x*`, `*bêta* *alpha*`, ``)`

**Arguments**

* *x*: Une valeur à laquelle évaluer la fonction.
* *alpha*: Un paramètre de la distribution.
* *bêta*: Un paramètre de la distribution.

**Retourne**

* La [fonction cumulative de distribution bêta](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function).

**Remarques**

Si un argument n’est pasnumérique, beta_cdf)renvoie la valeur nulle.

Si x < 0 ou x > 1, beta_cdf() retourne la valeur NaN.

Si l’alpha 0 ou la bêta 0, beta_cdf)retourne la valeur NaN.

**Exemples**

```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.9, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_cdf(x, alpha, beta)
```

|x|alpha|beta|comment|b|
|---|---|---|---|---|
|0.9|10|20|Entrée valide|0.999999999999959|
|1.5|10|20|x > 1, donne NaN|NaN|
|-10|10|20|x < 0, cède NaN|NaN|
|0.1|-1|20|alpha est < 0, donne NaN|NaN|


**Voir aussi**


* Pour calculer l’inverse de la fonction de densité de probabilité cumulative bêta, voir [bêta-inv()](./beta-invfunction.md).
* Pour la fonction de densité de probabilité de calcul, voir [bêta-pdf()](./beta-pdffunction.md).