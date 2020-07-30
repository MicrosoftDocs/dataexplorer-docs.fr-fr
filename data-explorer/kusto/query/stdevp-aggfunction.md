---
title: STDEVP () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit StDevP () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c5dffc8695df466dfc1ac9f0c5bcc4a40f687b2a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342714"
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

## <a name="returns"></a>Retourne

Valeur d’écart type de *expr* dans le groupe.
 
## <a name="examples"></a>Exemples

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[1, 2, 3, 4, 5]|1.4142135623731|