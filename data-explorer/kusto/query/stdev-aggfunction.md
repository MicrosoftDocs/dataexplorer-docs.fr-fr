---
title: StDev () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ECARTYPE () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d2ed328930f01d3f9c52a675eab61805fd87593
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247067"
---
# <a name="stdev-aggregation-function"></a>StDev () (fonction d’agrégation)

Calcule l’écart type de *expr* au sein du groupe, en considérant le groupe comme un [échantillon](https://en.wikipedia.org/wiki/Sample_%28statistics%29). 

* Formule utilisée :

:::image type="content" source="images/stdev-aggfunction/stdev-sample.png" alt-text="ECARTYPE, exemple":::

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `stdev(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 

## <a name="returns"></a>Retours

Valeur d’écart type de *expr* dans le groupe.
 
## <a name="examples"></a>Exemples

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1, 2, 3, 4, 5]|1.58113883008419|