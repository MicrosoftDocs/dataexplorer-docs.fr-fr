---
title: binary_all_or () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit binary_all_or () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: bff5d631493b9431d6e1a5fa88d3dd419147df96
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247967"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or () (fonction d’agrégation)

Accumule les valeurs à l’aide de l' `OR` opération binaire par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `binary_all_or(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: nombre long.

## <a name="returns"></a>Retours

Retourne une valeur agrégée à l’aide de l' `OR` opération binaire sur les enregistrements par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

## <a name="example"></a>Exemple

Production de « café-Food » à l’aide d’opérations binaires `OR` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0x88888008,
  0x42000000,
  0x00767000,
  0x00000005, 
]
| summarize result = toupper(tohex(binary_all_or(num)))
```

|result|
|---|
|CAFEF00D|
