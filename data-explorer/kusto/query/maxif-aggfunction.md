---
title: maxif () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit maxif () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4df30f50d82e0ad5af87acaaa88b55f151a2a77a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251715"
---
# <a name="maxif-aggregation-function"></a>maxif () (fonction d’agrégation)

Retourne la valeur maximale dans le groupe pour lequel le *prédicat* est évalué à `true` .

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

Voir aussi-fonction [Max ()](max-aggfunction.md) , qui retourne la valeur maximale dans le groupe sans expression de prédicat.

## <a name="syntax"></a>Syntaxe

`summarize``maxif(` *Expr*, `,` *prédicat*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 
* *Predicate*: prédicat qui, si la valeur est true, la valeur calculée *expr* est vérifiée pour le nombre maximal.

## <a name="returns"></a>Retours

Valeur maximale de *expr* dans le groupe pour lequel le *prédicat* est évalué à `true` .

## <a name="examples"></a>Exemples

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|