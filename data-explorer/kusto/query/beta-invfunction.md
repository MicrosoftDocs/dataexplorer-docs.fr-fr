---
title: beta_inv ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit beta_inv () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 60b054bbd234a77f81c47e375b98be0a5df103a5
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227653"
---
# <a name="beta_inv"></a>beta_inv()

Retourne l’inverse de la fonction de densité bêta cumulative de probabilité bêta.

```kusto
beta_inv(0.1, 10.0, 50.0)
```

Si *probabilité*  =  `beta_cdf(` *x*,... `)` , alors `beta_inv(` *probabilité*,... `)`  =  *x*. 

La distribution Bêta peut être utilisée dans la planification de projet pour modéliser les durées d’exécution probables pour une durée d’exécution attendue et une variabilité données.

**Syntaxe**

`beta_inv(`*probabilité* `, ` *alpha* `, ` *version bêta*`)`

**Arguments**

* *probabilité*: probabilité associée à la distribution bêta.
* *alpha*: paramètre de la distribution.
* *bêta*: paramètre de la distribution.

**Retourne**

* Inverse de la fonction de densité de probabilité cumulative bêta [beta_cdf ()](./beta-cdffunction.md)

**Remarques**

Si un argument n’est pas numérique, beta_inv () retourne une valeur null.

Si alpha ≤ 0 ou beta ≤ 0, beta_inv () retourne la valeur null.

Si la probabilité ≤ 0 ou la probabilité > 1, beta_inv () retourne la valeur NaN.

Pour une valeur de probabilité, beta_inv () recherche cette valeur x de sorte que beta_cdf (x, alpha, bêta) = probabilité.

**Exemples**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(p:double, alpha:double, beta:double, comment:string)
[
    0.1, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "p > 1, yields null",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_inv(p, alpha, beta)
```

|p|alpha|beta|comment|b|
|---|---|---|---|---|
|0.1|10|20|Entrée valide|0.226415022388749|
|1.5|10|20|p > 1, donne NULL||
|0.1|-1|20|Alpha est < 0, produit NaN|NaN|

**Voir aussi**

* Pour calculer la fonction de distribution cumulative bêta, consultez [beta-CDF ()](./beta-cdffunction.md).
* Pour le calcul de la fonction de densité bêta de probabilité, consultez [beta-PDF ()](./beta-pdffunction.md).
