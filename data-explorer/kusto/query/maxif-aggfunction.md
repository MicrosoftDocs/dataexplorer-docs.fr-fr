---
title: maxif() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit maxif() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9be25615f9da61aec6b4d56543f624fa0c24c1c4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512442"
---
# <a name="maxif-aggregation-function"></a>maxif() (fonction d’agrégation)

Retourne la valeur maximale dans l’ensemble du `true`groupe pour lequel *Predicate* évalue à .

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

Voir aussi - [fonction max(),](max-aggfunction.md) qui retourne la valeur maximale dans l’ensemble du groupe sans expression prédicative.

**Syntaxe**

`summarize``maxif(` *Expr*`,`Predicate Expr Predicate Expr*Predicate* Expr`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 
* *Prédicat : prédicez*que si c’est vrai, la valeur calculée *Expr* sera vérifiée au maximum.

**Retourne**

La valeur maximale d’Expr à travers le `true`groupe pour lequel *Predicate* évalue à . *Expr*

**Exemples**

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|