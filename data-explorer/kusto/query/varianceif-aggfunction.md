---
title: varianceif () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit varianceif () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf1009d2d269bf21ea5ae14a9c828724d8bf8c70
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338471"
---
# <a name="varianceif-aggregation-function"></a>varianceif () (fonction d’agrégation)

Calcule la [variance](variance-aggfunction.md) de *expr* sur le groupe pour lequel le *prédicat* a la valeur `true` .

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

synthétiser le `varianceif(` *Expr* `, ` *prédicat* expr`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 
* *Predicate*: prédicat qui, si la valeur est true, la valeur calculée *expr* sera ajoutée à la variance.

## <a name="returns"></a>Retourne

Valeur de variance de *expr* dans le groupe où le *prédicat* a la valeur `true` .
 
## <a name="examples"></a>Exemples

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|