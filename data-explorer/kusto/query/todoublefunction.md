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
ms.openlocfilehash: e62432773d99d74a46022cad3199f3bab0cae50b
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128453"
---
# <a name="todouble-toreal"></a>ToDouble (), TOREAL ()

Convertit l’entrée en une valeur de type `real` . ( `todouble()` et `toreal()` sont des synonymes).

```kusto
toreal("123.4") == 123.4
```

**Syntaxe**

`toreal(`*Expr* `)` 
 Expr `todouble(` *Expr*`)`

**Arguments**

* *Expr*: expression dont la valeur sera convertie en une valeur de type `real` .

**Retourne**

Si la conversion réussit, le résultat est une valeur de type `real` .
Si la conversion échoue, le résultat est la valeur `real(null)` .

*Remarque*: préférez utiliser [double () ou Real ()](./scalar-data-types/real.md) lorsque cela est possible.