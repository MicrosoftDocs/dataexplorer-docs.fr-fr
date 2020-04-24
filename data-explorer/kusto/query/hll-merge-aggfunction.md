---
title: hll_merge() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit hll_merge () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 4700d5c87bf0f29f7bba86d56114a6a61092da94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514108"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge))(fonction d’agrégation)

Fusionne les résultats HLL à travers le groupe en valeur HLL unique.

* Peut être utilisé uniquement dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md).

En savoir plus sur [l’algorithme sous-jacent (*H*yper*L*og*L*og) et la précision d’estimation](dcount-aggfunction.md#estimation-accuracy).

**Syntaxe**

`summarize``hll_merge(` *Expr Expr*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

Les valeurs de hll fusionnées *d’Expr* à travers le groupe.
 
**Conseils**

1) Vous pouvez utiliser la fonction [dcount_hll] (dcount-hllfunction.md) qui calculera le dcount à partir de fonctions d’agrégation de hll /hll-merge.