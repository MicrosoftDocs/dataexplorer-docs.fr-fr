---
title: stdevif() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit stdevif () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4a64cf1bb69860a2a8bd64de91cb00c2f0ec296f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506968"
---
# <a name="stdevif-aggregation-function"></a>stdevif() (fonction d’agrégation)

Calcule le [stdev](stdev-aggfunction.md) d’Expr à travers le `true`groupe pour lequel *Predicate* évalue à . *Expr*

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `stdevif(` *Expr*`, `*Predicate*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 
* *Prédicat : prédicez*que si c’est vrai, la valeur calculée *Expr* sera ajoutée à l’écart standard.

**Retourne**

La valeur d’écart standard d’Expr à travers `true`le groupe où *Predicate* évalue à . *Expr*
 
**Exemples**

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|