---
title: variancep() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le variancep() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 386244806a6fcb3f321eb1a6b40595dc71b2b413
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504588"
---
# <a name="variancep-aggregation-function"></a>variancep() (fonction d’agrégation)

Calcule l’écart *d’Expr* à travers le groupe, considérant le groupe comme une [population](https://en.wikipedia.org/wiki/Statistical_population). 

* Formule utilisée : ![texte alt](./images/aggregations/variance-population.png "variance-population")

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `variancep(` *Expr*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

La valeur de variance *d’Expr* à travers le groupe.
 
**Exemples**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[ 1, 2, 3, 4, 5]|2|