---
title: STDEVP () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit StDevP () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16ae0e297dacefb3a9cc8bc7efb579393d89c968
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243835"
---
# <a name="stdevp-aggregation-function"></a>STDEVP () (fonction d’agrégation)

Calcule l’écart type de *expr* au sein du groupe, en considérant le groupe comme un [remplissage](https://en.wikipedia.org/wiki/Statistical_population). 

* Formule utilisée :

:::image type="content" source="images/stdevp-aggfunction/stdev-population.png" alt-text="Remplissage ECARTYPE":::

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `stdevp(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 

## <a name="returns"></a>Retours

Valeur d’écart type de *expr* dans le groupe.
 
## <a name="examples"></a>Exemples

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[1, 2, 3, 4, 5]|1.4142135623731|