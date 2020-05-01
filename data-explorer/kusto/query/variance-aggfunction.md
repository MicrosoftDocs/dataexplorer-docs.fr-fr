---
title: variance () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la variance () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f8afae2413d65618cf6b6e2f400df2500b06078
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618041"
---
# <a name="variance-aggregation-function"></a>variance () (fonction d’agrégation)

Calcule la variance de *expr* dans le groupe, en considérant le groupe comme un [échantillon](https://en.wikipedia.org/wiki/Sample_%28statistics%29). 

* Formule utilisée :

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="Exemple variance":::

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

**Syntaxe**

`variance(`résumer *expr*`)`

**Arguments**

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

Valeur de variance de *expr* dans le groupe.
 
**Exemples**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2.5|