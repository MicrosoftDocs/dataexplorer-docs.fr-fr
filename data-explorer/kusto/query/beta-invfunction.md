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
ms.openlocfilehash: deb91e6131d5662017ebdf714a79d0ee391c8ba1
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103299"
---
# <a name="beta_inv"></a>beta_inv()

Retourne l’inverse de la fonction de densité bêta cumulative de probabilité bêta.

```kusto
beta_inv(0.1, 10.0, 50.0)
```

Si *probabilité*  =  `beta_cdf(` *x*,... `)` , alors `beta_inv(` *probabilité*,... `)`  =  *x*. 

La distribution Bêta peut être utilisée dans la planification de projet pour modéliser les durées d’exécution probables pour une durée d’exécution attendue et une variabilité données.

## <a name="syntax"></a>Syntaxe

`beta_inv(`*probabilité* `, ` *alpha* `, ` *version bêta*`)`

## <a name="arguments"></a>Arguments

* *probabilité*: probabilité associée à la distribution bêta.
* *alpha*: paramètre de la distribution.
* *bêta*: paramètre de la distribution.

## <a name="returns"></a>retourne :

* Inverse de la fonction de densité de probabilité cumulative bêta [beta_cdf ()](./beta-cdffunction.md)

**Remarques**

Si un argument n’est pas numérique, beta_inv () retourne une valeur null.

Si alpha ≤ 0 ou beta ≤ 0, beta_inv () retourne la valeur null.

Si la probabilité ≤ 0 ou la probabilité > 1, beta_inv () retourne la valeur NaN.

Pour une valeur de probabilité, beta_inv () recherche cette valeur x de sorte que beta_cdf (x, alpha, bêta) = probabilité.

## <a name="examples"></a>Exemples

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

|p|alpha|bêta|comment|b|
|---|---|---|---|---|
|0,1|10|20|Entrée valide|0.226415022388749|
|1.5|10|20|p > 1, donne NULL||
|0,1|-1|20|Alpha est < 0, produit NaN|NaN|

## <a name="see-also"></a>Voir aussi

* Pour calculer la fonction de distribution cumulative bêta, consultez [beta-CDF ()](./beta-cdffunction.md).
* Pour le calcul de la fonction de densité bêta de probabilité, consultez [beta-PDF ()](./beta-pdffunction.md).
