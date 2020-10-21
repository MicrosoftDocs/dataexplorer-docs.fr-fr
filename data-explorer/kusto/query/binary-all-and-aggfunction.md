---
title: binary_all_and () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit binary_all_and () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: ffc143a3e9e76ab2d822754cc5488c0c830eef89
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245395"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and () (fonction d’agrégation)

Accumule les valeurs à l’aide de l' `AND` opération binaire par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `binary_all_and(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: nombre long.

## <a name="returns"></a>Retours

Retourne une valeur agrégée à l’aide de l' `AND` opération binaire sur les enregistrements par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

## <a name="example"></a>Exemple

Production de « café-Food » à l’aide d’opérations binaires `AND` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0xFFFFFFFF, 
  0xFFFFF00F,
  0xCFFFFFFD,
  0xFAFEFFFF,
]
| summarize result = toupper(tohex(binary_all_and(num)))
```

|result|
|---|
|CAFEF00D|
