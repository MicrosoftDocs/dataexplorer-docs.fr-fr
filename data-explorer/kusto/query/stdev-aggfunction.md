---
title: stdev() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit stdev () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d54af583db6f7aca0b436040c453249207a5ae59
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663095"
---
# <a name="stdev-aggregation-function"></a>stdev() (fonction d’agrégation)

Calcule l’écart standard *d’Expr* dans l’ensemble du groupe, considérant le groupe comme un [échantillon](https://en.wikipedia.org/wiki/Sample_%28statistics%29). 

* Formule utilisée :

:::image type="content" source="images/aggregations/stdev-sample.png" alt-text="Échantillon de Stdev":::

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `stdev(` *Expr*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

La valeur d’écart standard *d’Expr* dans l’ensemble du groupe.
 
**Exemples**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[ 1, 2, 3, 4, 5]|1.58113883008419|