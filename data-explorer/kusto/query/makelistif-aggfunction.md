---
title: make_list_if() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit make_list_if () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b34c1dad7be709145c622c97b357734c25292bba
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512731"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if)) (fonction d’agrégation)

Retourne `dynamic` un tableau (JSON) de toutes les valeurs d’Expr dans `true`le groupe, pour lequel *Predicate* évalue à . *Expr*

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize``make_list_if(` *Expr*, *Predicate* [`,` *MaxSize*]`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation.
* *Predicate*: Predicate qui `true`doit évaluer à , afin d’Expr d’être ajouté au résultat. *Expr*
* *MaxSize* est une limite d’intégrisation facultative sur le nombre maximum d’éléments retournés (par défaut est *1048576*). La valeur MaxSize ne peut excéder 1048576.

**Retourne**

Retourne `dynamic` un tableau (JSON) de toutes les valeurs d’Expr dans `true`le groupe, pour lequel *Predicate* évalue à . *Expr*
Si l’entrée `summarize` à l’opérateur n’est pas triée, l’ordre des éléments dans le tableau résultant n’est pas défini.
Si l’entrée `summarize` à l’opérateur est triée, l’ordre des éléments dans le tableau résultant suit celui de l’entrée.

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
| summarize make_list_if(name, strlen(name) > 4)
```

|list_name|
|----|
|["George", "Ringo"]|

**Voir aussi**

[`make_list`](./makelist-aggfunction.md)fonction, qui fait la même chose, sans expression prédicat.