---
title: binary_all_and () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit binary_all_and () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9086d00ecbc800174ce2b9cda2b4ae1ba59d52b5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349140"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and () (fonction d’agrégation)

Accumule les valeurs à l’aide de l' `AND` opération binaire par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `binary_all_and(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: nombre long.

## <a name="returns"></a>Retourne

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
