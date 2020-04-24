---
title: binary_all_or() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit binary_all_or () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 5de240f606e53b26996ebfe11073170758233e2c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517712"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or)) (fonction d’agrégation)

Accumule les valeurs `OR` à l’aide de l’opération binaire par groupe de synthèse (ou au total, si la résumation se fait sans regroupement).

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `binary_all_or(` *Expr*`)`

**Arguments**

* *Expr*: long nombre.

**Retourne**

Retourne une valeur qui est `OR` agrégée à l’aide de l’opération binaire sur les enregistrements par groupe de synthèse (ou au total, si la résumation se fait sans regroupement).

**Exemple**

Produire des « café-food » à l’aide d’opérations binaires `OR` :

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