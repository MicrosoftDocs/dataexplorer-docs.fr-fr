---
title: binary_all_xor() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit binary_all_xor() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: a1908fe874576281c9ba45f23709845473b5725c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517695"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor)) (fonction d’agrégation)

Accumule les valeurs `XOR` à l’aide de l’opération binaire par groupe de synthèse (ou au total, si la résumation se fait sans regroupement).

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `binary_all_xor(` *Expr*`)`

**Arguments**

* *Expr*: long nombre.

**Retourne**

Retourne une valeur qui est `XOR` agrégée à l’aide de l’opération binaire sur les enregistrements par groupe de synthèse (ou au total, si la résumation se fait sans regroupement).

**Exemple**

Produire des « café-food » à l’aide d’opérations binaires `XOR` :

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