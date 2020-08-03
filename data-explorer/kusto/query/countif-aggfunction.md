---
title: NB.si () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit NB.si () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/02/2020
ms.openlocfilehash: 669978d8828f54926a8535f199ef7a9bc2ba7451
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515768"
---
# <a name="countif-aggregation-function"></a>NB.si () (fonction d’agrégation)

Retourne le nombre de lignes pour lesquelles *Predicate* a la valeur `true`.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

Voir également la fonction [Count ()](count-aggfunction.md) , qui compte les lignes sans expression de prédicat.

## <a name="syntax"></a>Syntaxe

synthétiser le `countif(` *prédicat*`)`

## <a name="arguments"></a>Arguments

*Predicate*: expression qui sera utilisée pour le calcul de l’agrégation. Le *prédicat* peut être n’importe quelle expression scalaire avec le type de retour bool (évaluation à true/false).

## <a name="returns"></a>Retours

Retourne le nombre de lignes pour lesquelles *Predicate* a la valeur `true`.

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
| summarize countif(strlen(name) > 4)
```

|countif_|
|----|
|2|

