---
title: sumif() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit sumif() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7d2c96f73b404e8d9acbe9da9defecd6bf1bbf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506662"
---
# <a name="sumif-aggregation-function"></a>sumif() (fonction d’agrégation)

Retourne une somme d’Expr pour laquelle `true` *Predicate* évalue à . *Expr*

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

Vous pouvez également utiliser la [fonction somme()](sum-aggfunction.md) qui résume les rangées sans expression prédicat.

**Syntaxe**

résumer `sumif(` *Expr*`,`*Predicate*`)`

**Arguments**

* *Expr*: expression pour calcul d’agrégation. 
* *Prédicat : prédicez*que, si c’est vrai, la valeur calculée de *l’Expr*sera ajoutée à la somme. 

**Retourne**

La valeur de somme d’Expr pour `true`laquelle *Predicate* évalue à . *Expr*

> [!TIP]
> Utiliser `summarize sumif(expr, filter)` à la place de `where filter | summarize sum(expr)`

**Exemple**

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize sumif(day_of_birth, strlen(name) > 4)
```

|sumif_day_of_birth|
|----|
|32|