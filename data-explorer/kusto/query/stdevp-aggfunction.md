---
title: stdevp() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit stdevp() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0549c15ec9e2435d242f210e6dfc2163796e5f39
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663206"
---
# <a name="stdevp-aggregation-function"></a>stdevp() (fonction d’agrégation)

Calcule l’écart standard *d’Expr* à travers le groupe, considérant le groupe comme une [population](https://en.wikipedia.org/wiki/Statistical_population). 

* Formule utilisée :

:::image type="content" source="images/aggregations/stdev-population.png" alt-text="Population de Stdev":::

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `stdevp(` *Expr*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

La valeur d’écart standard *d’Expr* dans l’ensemble du groupe.
 
**Exemples**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[ 1, 2, 3, 4, 5]|1.4142135623731|