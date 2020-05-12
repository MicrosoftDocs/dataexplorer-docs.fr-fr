---
title: binary_all_or () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit binary_all_or () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: e00d170db7cbafb36f04dfeb14f64caf2b8abcff
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225256"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or () (fonction d’agrégation)

Accumule les valeurs à l’aide de l' `OR` opération binaire par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

**Syntaxe**

résumer `binary_all_or(` *expr*`)`

**Arguments**

* *Expr*: nombre long.

**Retourne**

Retourne une valeur agrégée à l’aide de l' `OR` opération binaire sur les enregistrements par groupe de synthèse (ou au total, si le résumé est effectué sans regroupement).

**Exemple**

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
