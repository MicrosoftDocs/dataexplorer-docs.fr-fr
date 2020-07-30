---
title: stdevif () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit stdevif () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a158a623768a7beb6ec497ca8d8467aecd7c3b61
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342816"
---
# <a name="stdevif-aggregation-function"></a>stdevif () (fonction d’agrégation)

Calcule la [ECARTYPE](stdev-aggfunction.md) de *expr* dans le groupe pour lequel le *prédicat* a la valeur `true` .

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

synthétiser le `stdevif(` *Expr* `, ` *prédicat* expr`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 
* *Predicate*: prédicat qui, si la valeur est true, la valeur calculée *expr* sera ajoutée à l’écart type.

## <a name="returns"></a>Retourne

Valeur d’écart type de *expr* dans le groupe où le *prédicat* prend la valeur `true` .
 
## <a name="examples"></a>Exemples

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|