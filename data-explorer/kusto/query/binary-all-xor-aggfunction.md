---
title: binary_all_xor () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit binary_all_xor () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 7aa83f7c214c7bc45892ff1064a09dd84240b5f4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246726"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor () (fonction d’agrégation)

Accumule les valeurs à l’aide de l' `XOR` opération binaire par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `binary_all_xor(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: nombre long.

## <a name="returns"></a>Retours

Retourne une valeur agrégée à l’aide de l' `XOR` opération binaire sur les enregistrements par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

## <a name="example"></a>Exemple

Production de « café-Food » à l’aide d’opérations binaires `XOR` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0x44404440,
  0x1E1E1E1E,
  0x90ABBA09,
  0x000B105A,
]
| summarize result = toupper(tohex(binary_all_xor(num)))
```

|result|
|---|
|CAFEF00D|
