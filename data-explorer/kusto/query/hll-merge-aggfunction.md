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
ms.openlocfilehash: 4681f92155181f85cad5c46ed70a79cb173d437f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347542"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (fonction d’agrégation)

Fusionne `HLL` les résultats dans le groupe en une seule `HLL` valeur.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md).

Pour plus d’informations, voir [algorithme sous-jacent (*H*yper*l*og*l*) et précision](dcount-aggfunction.md#estimation-accuracy)de l’estimation.

## <a name="syntax"></a>Syntaxe

`summarize``hll_merge(` *Expr*`)`

## <a name="arguments"></a>Arguments

* `*Expr*`: Expression qui sera utilisée pour le calcul de l’agrégation.

## <a name="returns"></a>Retourne

La fonction retourne les valeurs fusionnées `hll` de `*Expr*` dans le groupe.
 
**Conseils**

1) Utilisez la fonction [dcount_hll] (DCount-hllfunction.MD) pour calculer le `dcount` à partir des `hll`  /  `hll-merge` fonctions d’agrégation.
