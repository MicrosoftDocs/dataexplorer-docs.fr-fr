---
title: opérateur de sérialisation-Azure Explorateur de données
description: Cet article décrit l’opérateur Serialize dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 708a5ccd5f8402dedb074a6ab8c17b1d7762839c
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717119"
---
# <a name="serialize-operator"></a>serialize, opérateur

Marque que l’ordre de l’ensemble de lignes d’entrée peut être utilisé en toute sécurité pour les fonctions de fenêtre.

L’opérateur a une signification déclarative. Elle marque l’ensemble de lignes d’entrée comme étant sérialisé (ordonné), afin que les [fonctions de fenêtre](./windowsfunctions.md) puissent être appliquées à celle-ci.

```kusto
T | serialize rn=row_number()
```

**Syntaxe**

`serialize`[*Nom1* `=` *Expr1* [ `,` *nom2* `=` *expr2*]...]

* Les paires *nom* / *expr* sont similaires à celles de l' [opérateur Extend](./extendoperator.md).

**Exemple**

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
