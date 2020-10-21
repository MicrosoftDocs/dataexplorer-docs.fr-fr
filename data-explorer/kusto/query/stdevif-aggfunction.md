---
title: stdevif () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit stdevif () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 831011bcd72c2fc9b06c3c68c6ebda7929d49e4b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242532"
---
# <a name="stdevif-aggregation-function"></a>stdevif () (fonction d’agrégation)

Calcule la [ECARTYPE](stdev-aggfunction.md) de *expr* dans le groupe pour lequel le *prédicat* a la valeur `true` .

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

synthétiser le `stdevif(` *Expr* `, ` *prédicat* expr`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 
* *Predicate*: prédicat qui, si la valeur est true, la valeur calculée *expr* sera ajoutée à l’écart type.

## <a name="returns"></a>Retours

Valeur d’écart type de *expr* dans le groupe où le *prédicat* prend la valeur `true` .
 
## <a name="examples"></a>Exemples

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|