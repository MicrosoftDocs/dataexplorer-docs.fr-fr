---
title: beta_pdf() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit beta_pdf() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53b86d88b05cc6c5cc31f1e3bbb9e3e712eed7f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517865"
---
# <a name="beta_pdf"></a>beta_pdf()

Retourne la fonction bêta de densité de probabilité.

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

La distribution Bêta est couramment utilisée pour étudier la variation du pourcentage d’une valeur dans des échantillons. Il peut s’agir, par exemple, de la fraction d’une journée que des gens passent à regarder la télévision.

**Syntaxe**

`beta_pdf(`*x*`, `*bêta* *alpha*`, ``)`

**Arguments**

* *x*: Une valeur à laquelle évaluer la fonction.
* *alpha*: Un paramètre de la distribution.
* *bêta*: Un paramètre de la distribution.

**Retourne**

* La [fonction de densité bêta de probabilité](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function).

**Remarques**

Si un argument n’est pasnumérique, beta_pdf)renvoie la valeur nulle.

Si x x x 0 ou 1 x, beta_pdf)renvoie la valeur NaN.

Si l’alpha 0 ou la bêta 0, beta_pdf)retourne la valeur NaN.

**Exemples**

```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.5, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend r = beta_pdf(x, alpha, beta)
```

|x|alpha|beta|comment|r|
|---|---|---|---|---|
|0.5|10|20|Entrée valide|0.746176019310951|
|1.5|10|20|x > 1, donne NaN|NaN|
|-10|10|20|x < 0, cède NaN|NaN|
|0.1|-1|20|alpha est < 0, donne NaN|NaN|

**Informations de référence**

* Pour calculer l’inverse de la fonction de densité de probabilité cumulative bêta, voir [bêta-inv()](./beta-invfunction.md).
* Pour la fonction de distribution bêta cumulative standard, voir [bêta-cdf()](./beta-cdffunction.md).