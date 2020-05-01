---
title: VarianceP () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit VarianceP () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 80f3f900649d2c4c36c7a50831e011f0ee018860
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618006"
---
# <a name="variancep-aggregation-function"></a>VarianceP () (fonction d’agrégation)

Calcule la variance de *expr* dans le groupe, en tenant compte du groupe comme [population](https://en.wikipedia.org/wiki/Statistical_population). 

* Formule utilisée :

:::image type="content" source="images/variancep-aggfunction/variance-population.png" alt-text="Population de variances":::

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

**Syntaxe**

`variancep(`résumer *expr*`)`

**Arguments**

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

Valeur de variance de *expr* dans le groupe.
 
**Exemples**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2|