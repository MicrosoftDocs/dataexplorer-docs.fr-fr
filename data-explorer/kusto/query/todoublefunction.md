---
title: todouble()/toreal() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit todouble()/toreal() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb9c976f1646f71fcf8b345899037461f58f4ef0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506322"
---
# <a name="todoubletoreal"></a>todouble()/toreal()

Convertit l’entrée en `real`valeur de type . (`todouble()` `toreal()` et sont synonymes.)

```kusto
toreal("123.4") == 123.4
```

**Syntaxe**

`toreal(`*Expr*`)`
`todouble(`*Expr Expr Expr Expr Expr Expr*`)`

**Arguments**

* *Expr*: Une expression dont la valeur sera `real`convertie en valeur de type .

**Retourne**

Si la conversion est réussie, le `real`résultat est une valeur de type .
Si la conversion n’est pas `real(null)`réussie, le résultat est la valeur .

*Remarque*: Préférez utiliser [le double () ou réel ()](./scalar-data-types/real.md) lorsque c’est possible.