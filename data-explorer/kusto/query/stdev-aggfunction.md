---
title: StDev () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ECARTYPE () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 18722a544ea3bbd0e19922d1d8988a3604b4d200
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342901"
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

## <a name="returns"></a>Retourne

Valeur d’écart type de *expr* dans le groupe.
 
## <a name="examples"></a>Exemples

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1, 2, 3, 4, 5]|1.58113883008419|