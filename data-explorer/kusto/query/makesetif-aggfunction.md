---
title: make_set_if() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit make_set_if () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1393e063fb0abb91b38a8b9e1edc0110e78b3638
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512646"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if)) (fonction d’agrégation)

Retourne `dynamic` un tableau (JSON) de l’ensemble de valeurs distinctes *qu’Expr* prend `true`dans le groupe, pour lequel *Predicate* évalue à .

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize``make_set_if(` *Expr*, *Predicate* [`,` *MaxSize*]`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation.
* *Predicate*: Predicate qui `true` doit évaluer pour *Expr* à ajouter au résultat.
* *MaxSize* est une limite d’intégrisation facultative sur le nombre maximum d’éléments retournés (par défaut est *1048576*). La valeur MaxSize ne peut excéder 1048576.

**Retourne**

Retourne `dynamic` un tableau (JSON) de l’ensemble de valeurs distinctes *qu’Expr* prend `true`dans le groupe, pour lequel *Predicate* évalue à .
L’ordre de tri du tableau n’est pas défini.

> [!TIP]
> Pour ne compter que les valeurs distinctes, utilisez [dcountif()](dcountif-aggfunction.md)

**Voir aussi**

[`make_set`](./makeset-aggfunction.md)fonction, qui fait la même chose, sans expression prédicat.

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
| summarize make_set_if(name, strlen(name) > 4)
```

|set_name|
|----|
|["George", "Ringo"]|