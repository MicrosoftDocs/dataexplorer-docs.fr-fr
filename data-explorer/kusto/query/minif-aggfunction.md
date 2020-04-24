---
title: minif() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit minif() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0aad254ec01e83bdb07734e5b309c1450512b446
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512357"
---
# <a name="minif-aggregation-function"></a>minif() (fonction d’agrégation)

Retourne la valeur minimale dans l’ensemble du `true`groupe pour lequel *Predicate* évalue à .

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

Voir aussi - [min()](min-aggfunction.md) fonction, qui retourne la valeur minimale à travers le groupe sans expression prédicative.

**Syntaxe**

`summarize``minif(` *Expr*`,`Predicate Expr Predicate Expr*Predicate* Expr`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation.
* *Prédicat : prédicez*que si c’est vrai, la valeur calculée *Expr* sera vérifiée au minimum.

**Retourne**

La valeur minimale d’Expr à travers le `true`groupe pour lequel *Predicate* évalue à . *Expr*

**Exemples**

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|