---
title: hll_merge () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit hll_merge () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 59813656fce6afea3ecba62b13c971e74b095fe1
ms.sourcegitcommit: 42cc7d11f41a5bfa9e021764b044dcd68d99a258
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93403712"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (fonction d’agrégation)

Fusionne `HLL` les résultats dans le groupe en une seule `HLL` valeur.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md).

Pour plus d’informations, voir [algorithme sous-jacent ( *H* yper *l* og *l* ) et précision](dcount-aggfunction.md#estimation-accuracy)de l’estimation.

## <a name="syntax"></a>Syntaxe

`summarize``hll_merge(` *Expr*`)`

## <a name="arguments"></a>Arguments

* `*Expr*`: Expression qui sera utilisée pour le calcul de l’agrégation.

## <a name="returns"></a>Retours

La fonction retourne les valeurs fusionnées `hll` de `*Expr*` dans le groupe.
 
**Conseils**

1) Utilisez la fonction [dcount_hll](dcount-hllfunction.md) pour calculer les `dcount` à partir des `hll`  /  `hll-merge` fonctions d’agrégation.
