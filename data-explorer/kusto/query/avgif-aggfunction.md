---
title: avgif () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit avgif () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e0d112554ff77cb9c591f787bbb62f9a92e3b19d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248020"
---
# <a name="avgif-aggregation-function"></a>avgif () (fonction d’agrégation)

Calcule la [moyenne](avg-aggfunction.md) de *expr* sur le groupe pour lequel le *prédicat* a la valeur `true` .

* Peut uniquement être utilisé dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

synthétiser le `avgif(` *Expr* `, ` *prédicat* expr`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. Les enregistrements avec des `null` valeurs sont ignorés et ne sont pas inclus dans le calcul.
* *Predicate*: prédicat qui, si la valeur est true, la valeur calculée *expr* sera ajoutée à la moyenne.

## <a name="returns"></a>Retours

Valeur moyenne de *expr* dans le groupe dont le *prédicat* est évalué à `true` .
 
## <a name="examples"></a>Exemples

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|