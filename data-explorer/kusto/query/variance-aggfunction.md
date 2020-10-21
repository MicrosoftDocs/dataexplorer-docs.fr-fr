---
title: variance () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la variance () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a031efb5068e4497df0fa7acb54561c3b3caffb
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92240945"
---
# <a name="variance-aggregation-function"></a>variance () (fonction d’agrégation)

Calcule la variance de *expr* dans le groupe, en considérant le groupe comme un [échantillon](https://en.wikipedia.org/wiki/Sample_%28statistics%29). 

* Formule utilisée :

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="Exemple variance":::

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `variance(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 

## <a name="returns"></a>Retours

Valeur de variance de *expr* dans le groupe.
 
## <a name="examples"></a>Exemples

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2.5|