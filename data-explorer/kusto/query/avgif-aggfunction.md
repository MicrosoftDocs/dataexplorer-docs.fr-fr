---
title: avgif () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit avgif () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 587af53de774332db70ef9bffcadf74d9e2c069d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349378"
---
# <a name="avgif-aggregation-function"></a>avgif () (fonction d’agrégation)

Calcule la [moyenne](avg-aggfunction.md) de *expr* sur le groupe pour lequel le *prédicat* a la valeur `true` .

* Peut uniquement être utilisé dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

synthétiser le `avgif(` *Expr* `, ` *prédicat* expr`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. Les enregistrements avec des `null` valeurs sont ignorés et ne sont pas inclus dans le calcul.
* *Predicate*: prédicat qui, si la valeur est true, la valeur calculée *expr* sera ajoutée à la moyenne.

## <a name="returns"></a>Retourne

Valeur moyenne de *expr* dans le groupe dont le *prédicat* est évalué à `true` .
 
## <a name="examples"></a>Exemples

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|