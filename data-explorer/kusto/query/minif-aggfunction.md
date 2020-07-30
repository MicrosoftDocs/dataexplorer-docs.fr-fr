---
title: miniF () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit miniF () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 91764aeb8c825a272c414df7a0572d3b8310e79f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346743"
---
# <a name="minif-aggregation-function"></a>miniF () (fonction d’agrégation)

Retourne la valeur minimale sur le groupe pour lequel le *prédicat* prend la valeur `true` .

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

Voir également la fonction- [min ()](min-aggfunction.md) , qui retourne la valeur minimale dans le groupe sans expression de prédicat.

## <a name="syntax"></a>Syntaxe

`summarize``minif(` *Expr*, `,` *prédicat*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation.
* *Predicate*: prédicat qui, si la valeur est true, la valeur calculée *expr* est vérifiée au minimum.

## <a name="returns"></a>Retourne

Valeur minimale de *expr* au sein du groupe pour lequel le *prédicat* prend la valeur `true` .

## <a name="examples"></a>Exemples

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|