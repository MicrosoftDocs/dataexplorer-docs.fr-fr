---
title: ToDouble ()/TOREAL ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ToDouble ()/TOREAL () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 34734f3975b1720c1d009f190d4fae2ebc54283f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350755"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

Convertit l’entrée en une valeur de type `real` . ( `todouble()` et `toreal()` sont des synonymes).

```kusto
toreal("123.4") == 123.4
```

## <a name="syntax"></a>Syntaxe

`toreal(`*Expr* `)` 
 Expr `todouble(` *Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression dont la valeur sera convertie en une valeur de type `real` .

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat est une valeur de type `real` .
Si la conversion échoue, le résultat est la valeur `real(null)` .

*Remarque*: préférez utiliser [double () ou Real ()](./scalar-data-types/real.md) lorsque cela est possible.