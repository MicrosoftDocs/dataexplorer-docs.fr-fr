---
title: binary_all_xor () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit binary_all_xor () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: bc4b0bc8a02dd3a8d2a39ffdd27db5817eb8ffdb
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225239"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor () (fonction d’agrégation)

Accumule les valeurs à l’aide de l' `XOR` opération binaire par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

**Syntaxe**

résumer `binary_all_xor(` *expr*`)`

**Arguments**

* *Expr*: nombre long.

**Retourne**

Retourne une valeur agrégée à l’aide de l' `XOR` opération binaire sur les enregistrements par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

**Exemple**

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
