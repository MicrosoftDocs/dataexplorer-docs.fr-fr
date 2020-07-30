---
title: tdigest_merge () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit tdigest_merge () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 6f15e0028bda40a2d65349a7840861c9060ff805
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87341242"
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