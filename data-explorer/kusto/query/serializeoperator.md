---
title: opérateur de sérialisation-Azure Explorateur de données
description: Cet article décrit l’opérateur Serialize dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 603d69be34ea6040f6c864958eea86e570204d04
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250182"
---
# <a name="serialize-operator"></a>serialize, opérateur

Marque que l’ordre de l’ensemble de lignes d’entrée peut être utilisé en toute sécurité pour les fonctions de fenêtre.

L’opérateur a une signification déclarative. Elle marque l’ensemble de lignes d’entrée comme étant sérialisé (ordonné), afin que les [fonctions de fenêtre](./windowsfunctions.md) puissent être appliquées à celle-ci.

```kusto
T | serialize rn=row_number()
```

## <a name="syntax"></a>Syntaxe

`serialize` [*Nom1* `=` *Expr1* [ `,` *nom2* `=` *expr2*]...]

* Les paires *nom* / *expr* sont similaires à celles de l' [opérateur Extend](./extendoperator.md).

## <a name="example"></a>Exemple

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

L’ensemble de lignes de sortie des opérateurs suivants est marqué comme étant sérialisé.

[plage](./rangeoperator.md), [Tri](./sortoperator.md), [ordre](./orderoperator.md), [haut](./topoperator.md), [haut-Hitters](./tophittersoperator.md), [GetSchema](./getschemaoperator.md).

L’ensemble de lignes de sortie des opérateurs suivants est marqué comme non sérialisé.

[Sample](./sampleoperator.md), [Sample-distinct](./sampledistinctoperator.md), [distinct](./distinctoperator.md), [join](./joinoperator.md), [combriqued](./topnestedoperator.md), [Count](./countoperator.md), [Resume](./summarizeoperator.md), [facette](./facetoperator.md), [MV-Expand](./mvexpandoperator.md), [Evaluate](./evaluateoperator.md), [reduire par](./reduceoperator.md), [Make-Series](./make-seriesoperator.md)

Tous les autres opérateurs conservent la propriété de sérialisation. Si l’ensemble de lignes d’entrée est sérialisé, l’ensemble de lignes de sortie est également sérialisé.
