---
title: beta_inv() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit beta_inv() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 20bdf8bfc01ef3ac6c6a12f6a43d87fd7b5c07e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517882"
---
# <a name="beta_inv"></a>beta_inv()

Retourne l’inverse de la fonction bêta de densité bêta de probabilité cumulée bêta bêta bêta bêta.

```kusto
beta_inv(0.1, 10.0, 50.0)
```

Si *la probabilité* = `beta_cdf(`*x*,... `)`, `beta_inv(`puis *la probabilité*,... `)` *x*x  = . 

La distribution Bêta peut être utilisée dans la planification de projet pour modéliser les durées d’exécution probables pour une durée d’exécution attendue et une variabilité données.

**Syntaxe**

`beta_inv(`*probabilité*`, `*alpha*`, `*bêta*`)`

**Arguments**

* *probabilité*: Probabilité associée à la distribution bêta.
* *alpha*: Un paramètre de la distribution.
* *bêta*: Un paramètre de la distribution.

**Retourne**

* L’inverse de la fonction de densité de probabilité cumulative bêta [beta_cdf()](./beta-cdffunction.md)

**Remarques**

Si un argument n’est pasnumérique, beta_inv)renvoie la valeur nulle.

Si l’alpha 0 ou la bêta 0, beta_inv)renvoie la valeur nulle.

Si la probabilité de 0 ou de probabilité > 1, beta_inv)retourne la valeur NaN.

Compte tenu d’une valeur pour la probabilité, beta_inv() cherche cette valeur x telle que beta_cdf (x, alpha, bêta) - probabilité.

**Exemples**

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
|1.5|10|20|p > 1, rendements nuls||
|0.1|-1|20|alpha est < 0, donne NaN|NaN|

**Voir aussi**

* Pour calculer la fonction cumulative de distribution bêta, voir [bêta-cdf()](./beta-cdffunction.md).
* Pour la fonction de densité bêta de probabilité de calcul, voir [bêta-pdf()](./beta-pdffunction.md).