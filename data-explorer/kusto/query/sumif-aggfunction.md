---
title: somme.si () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit somme.si () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cd9900b5087ed0d6ae7e97d2f2dad809bb909331
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350806"
---
# <a name="sumif-aggregation-function"></a>somme.si () (fonction d’agrégation)

Retourne la somme de *expr* pour laquelle le *prédicat* a la valeur `true` .

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

Vous pouvez également utiliser la fonction [Sum ()](sum-aggfunction.md) , qui additionne des lignes sans expression de prédicat.

## <a name="syntax"></a>Syntaxe

synthétiser le `sumif(` *Expr* `,` *prédicat* expr`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression pour le calcul de l’agrégation. 
* *Predicate*: prédicat qui, si la valeur est true, la valeur calculée de l' *expression expr*sera ajoutée à la somme. 

## <a name="returns"></a>Retours

Valeur SUM de *expr* pour laquelle le *prédicat* prend la valeur `true` .

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
| summarize sumif(day_of_birth, strlen(name) > 4)
```

|sumif_day_of_birth|
|----|
|32|