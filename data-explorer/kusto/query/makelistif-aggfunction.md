---
title: make_list_if () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit make_list_if () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 362fd1b64ba156115515efa3db3214b0f86c5ad7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241377"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if () (fonction d’agrégation)

Retourne un `dynamic` tableau (JSON) de toutes les valeurs de *expr* dans le groupe, pour lequel le *prédicat* a la valeur `true` .

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

`summarize``make_list_if(` *Expr*, *prédicat* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation.
* *Predicate*: prédicat qui doit être évalué à, pour que `true` *expr* soit ajouté au résultat.
* *MaxSize* est une limite d’entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *1048576*). La valeur MaxSize ne peut pas dépasser 1048576.

## <a name="returns"></a>Retours

Retourne un `dynamic` tableau (JSON) de toutes les valeurs de *expr* dans le groupe, pour lequel le *prédicat* a la valeur `true` .
Si l’entrée de l' `summarize` opérateur n’est pas triée, l’ordre des éléments dans le tableau résultant n’est pas défini.
Si l’entrée de l' `summarize` opérateur est triée, l’ordre des éléments dans le tableau résultant suit celui de l’entrée.

## <a name="example"></a>Exemple

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

## <a name="see-also"></a>Voir aussi

[`make_list`](./makelist-aggfunction.md) fonction, qui fait la même chose, sans expression de prédicat.