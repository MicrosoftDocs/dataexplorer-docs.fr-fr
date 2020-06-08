---
title: hll_merge () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit hll_merge () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 59c6f6a11b108cf6e74ceb59d3483ea1a95f7002
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512382"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (fonction d’agrégation)

Fusionne `HLL` les résultats dans le groupe en une seule `HLL` valeur.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md).

Pour plus d’informations, voir [algorithme sous-jacent (*H*yper*l*og*l*) et précision](dcount-aggfunction.md#estimation-accuracy)de l’estimation.

**Syntaxe**

`summarize``hll_merge(` *Expr*`)`

**Arguments**

* `*Expr*`: Expression qui sera utilisée pour le calcul de l’agrégation.

**Retourne**

La fonction retourne les valeurs fusionnées `hll` de `*Expr*` dans le groupe.
 
**Conseils**

1) Utilisez la fonction [dcount_hll] (DCount-hllfunction.MD) pour calculer le `dcount` à partir des `hll`  /  `hll-merge` fonctions d’agrégation.
