---
title: opérateur de sérialisation - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de sérialisation dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5402d1e1fcceb42f02643bf24918ed07beddaed7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509110"
---
# <a name="serialize-operator"></a>serialize, opérateur

Marque que l’ordre de l’ensemble de ligne d’entrée est sûr pour l’utilisation des fonctions de fenêtre.

L’opérateur a une signification déclarative, et il marque la ligne d’entrée définie comme sérialisée (commandée) afin que [les fonctions de fenêtre](./windowsfunctions.md) puissent être appliquées à elle.

```kusto
T | serialize rn=row_number()
```

**Syntaxe**

`serialize`[*Nom1* `=` *Expr1* [`,` *Name2* `=` *Expr2*]...]

* Les paires *Name*/*Expr* sont similaires à celles de l’opérateur [d’extension.](./extendoperator.md)

**Exemple**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

L’ensemble de la ligne de sortie des opérateurs suivants est marqué comme sérialisé :

[gamme](./rangeoperator.md), [trier](./sortoperator.md), [ordre](./orderoperator.md), [haut](./topoperator.md), [top-hitters](./tophittersoperator.md), [getschema](./getschemaoperator.md).

L’ensemble de la ligne de sortie des opérateurs suivants est marqué comme non-sérialisé :

[échantillon](./sampleoperator.md), [échantillon distinct](./sampledistinctoperator.md), [distinct](./distinctoperator.md), [rejoindre](./joinoperator.md), [top-nested](./topnestedoperator.md), [compter](./countoperator.md), [résumer](./summarizeoperator.md), [facette](./facetoperator.md), [mv-expand](./mvexpandoperator.md), [évaluer](./evaluateoperator.md), [réduire par](./reduceoperator.md), [make-series](./make-seriesoperator.md)

Tous les autres opérateurs préservent la propriété de sérialisation (si l’ensemble de la ligne d’entrée est sérialisé, il en va de même pour l’ensemble de la ligne de sortie).