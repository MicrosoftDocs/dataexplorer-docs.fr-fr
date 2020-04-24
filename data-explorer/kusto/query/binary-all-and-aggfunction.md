---
title: binary_all_and() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit binary_all_and () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 4dfe4a2881f100a4bea3e9d418022c75b2621087
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517746"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and)) (fonction d’agrégation)

Accumule les valeurs `AND` à l’aide de l’opération binaire par groupe de synthèse (ou au total, si la résumation se fait sans regroupement).

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `binary_all_and(` *Expr*`)`

**Arguments**

* *Expr*: long nombre.

**Retourne**

Retourne une valeur qui est `AND` agrégée à l’aide de l’opération binaire sur les enregistrements par groupe de synthèse (ou au total, si la résumation se fait sans regroupement).

**Exemple**

Produire des « café-food » à l’aide d’opérations binaires `AND` :

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