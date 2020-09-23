---
title: make_set_if () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit make_set_if () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 262676e2ee40b619c4984a23c23818c493aad47a
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103047"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if () (fonction d’agrégation)

Retourne un `dynamic` tableau (JSON) de l’ensemble de valeurs distinctes que *expr* prend dans le groupe, pour lequel le *prédicat* a la valeur `true` .

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

`summarize``make_set_if(` *Expr*, *prédicat* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation.
* *Predicate*: prédicat qui doit être évalué à pour que `true` *expr* soit ajouté au résultat.
* *MaxSize* est une limite d’entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *1048576*). La valeur MaxSize ne peut pas dépasser 1048576.

## <a name="returns"></a>retourne :

Retourne un `dynamic` tableau (JSON) de l’ensemble de valeurs distinctes que *expr* prend dans le groupe, pour lequel le *prédicat* a la valeur `true` .
L’ordre de tri du tableau n’est pas défini.

> [!TIP]
> Pour compter uniquement les valeurs distinctes, utilisez [dcountif ()](dcountif-aggfunction.md)

## <a name="see-also"></a>Voir aussi

[`make_set`](./makeset-aggfunction.md) fonction, qui fait la même chose, sans expression de prédicat.

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
| summarize make_set_if(name, strlen(name) > 4)
```

|set_name|
|----|
|["George", "Ringo"]|