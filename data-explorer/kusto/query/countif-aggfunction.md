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
ms.openlocfilehash: 9e81b4947f3a3a0b1102256cb7fd2f635ce4611b
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614971"
---
# <a name="countif-aggregation-function"></a>NB.si () (fonction d’agrégation)

Retourne le nombre de lignes pour lesquelles *Predicate* a la valeur `true`. Ne peut être utilisé que dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md).

## <a name="syntax"></a>Syntaxe

synthétiser le `countif(` *prédicat*`)`

## <a name="arguments"></a>Arguments

*Predicate*: expression qui sera utilisée pour le calcul de l’agrégation. Le *prédicat* peut être n’importe quelle expression scalaire avec le type de retour bool (évaluation à true/false).

## <a name="returns"></a>retourne :

Retourne le nombre de lignes pour lesquelles *Predicate* a la valeur `true`.

## <a name="example"></a> Exemple

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

## <a name="see-also"></a>Voir aussi

fonction [Count ()](count-aggfunction.md) , qui compte les lignes sans expression de prédicat.
