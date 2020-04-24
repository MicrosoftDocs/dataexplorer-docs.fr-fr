---
title: avgif() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit avgif () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61352be628b7c5a05085c092d0c022deaa0d9b6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518256"
---
# <a name="avgif-aggregation-function"></a>avgif() (fonction d’agrégation)

Calcule la [moyenne](avg-aggfunction.md) d’Expr à travers le `true`groupe pour lequel *Predicate* évalue à . *Expr*

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `avgif(` *Expr*`, `*Predicate*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. Les `null` enregistrements avec valeurs sont ignorés et ne sont pas inclus dans le calcul.
* *Prédicat : prédicez*que si c’est vrai, la valeur calculée *Expr* sera ajoutée à la moyenne.

**Retourne**

La valeur moyenne d’Expr à travers le `true`groupe où *Predicate* évalue à . *Expr*
 
**Exemples**

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|