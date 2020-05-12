---
title: beta_cdf ()-Azure Explorateur de données
description: Cet article décrit beta_cdf () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 76b16098d9340a98fb3a456dfa947c089507da6c
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227687"
---
# <a name="beta_cdf"></a>beta_cdf()

Retourne la fonction de distribution bêta cumulative standard.

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

Si *probabilité*  =  `beta_cdf(` *x*,... `)` , alors `beta_inv(` *probabilité*,... `)`  =  *x*.

La distribution Bêta est couramment utilisée pour étudier la variation du pourcentage d’une valeur dans des échantillons. Il peut s’agir, par exemple, de la fraction d’une journée que des gens passent à regarder la télévision.

**Syntaxe**

`beta_cdf(`*x* `, ` *alpha* `, ` *version bêta*`)`

**Arguments**

* *x*: valeur à laquelle évaluer la fonction.
* *alpha*: paramètre de la distribution.
* *bêta*: paramètre de la distribution.

**Retourne**

* [Fonction de distribution cumulative bêta](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function).

**Remarques**

Si un argument n’est pas numérique, beta_cdf () retourne une valeur null.

Si x < 0 ou x > 1, beta_cdf () retourne une valeur NaN.

Si alpha ≤ 0 ou beta ≤ 0, beta_cdf () retourne la valeur NaN.

**Exemples**

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|1.5|10|20|x > 1, produit NaN|NaN|
|-10|10|20|x < 0, produit NaN|NaN|
|0.1|-1|20|Alpha est < 0, produit NaN|NaN|


**Voir aussi**


* Pour calculer l’inverse de la fonction de densité de probabilité cumulative bêta, consultez [beta-INV ()](./beta-invfunction.md).
* Pour le calcul de la fonction de densité de probabilité, consultez [beta-PDF ()](./beta-pdffunction.md).