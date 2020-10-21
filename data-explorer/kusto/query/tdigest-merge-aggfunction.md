---
title: tdigest_merge () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit tdigest_merge () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 14b6b1c5ed31c312065e7641783bd1dc524993d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250636"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge () (fonction d’agrégation)

Fusionne les résultats de tdigest dans le groupe. 

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md).

En savoir plus sur l’algorithme sous-jacent (T-Digest) et l’erreur estimée [ici](percentiles-aggfunction.md#estimation-error-in-percentiles).

## <a name="syntax"></a>Syntaxe

Résumez `tdigest_merge(` *expr* `)` .

résumer `tdigest_merge(` *expr* `)` -un alias.

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 

## <a name="returns"></a>Retours

Valeurs tdigest fusionnées de *expr* dans le groupe.
 

**Conseils**

1) Vous pouvez utiliser la fonction [ `percentile_tdigest()` ] (percentile-tdigestfunction.MD).

2) Tous les tdigests inclus dans le même groupe doivent être du même type.