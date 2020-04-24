---
title: todecimal() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit todecimal() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d5ac70dfe71f80c3963292516e1b8516a297875
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506305"
---
# <a name="todecimal"></a>todecimal()

Convertit l’entrée en représentation décimale des nombres.

```kusto
todecimal("123.45678") == decimal(123.45678)
```

**Syntaxe**

`todecimal(`*Expr*`)`

**Arguments**

* *Expr*: Expression qui sera convertie en décimale. 

**Retourne**

Si la conversion est réussie, le résultat sera un nombre décimal.
Si la conversion n’est `null`pas réussie, le résultat sera .
 
*Remarque*: Préférez utiliser [réel()](./scalar-data-types/real.md) lorsque c’est possible.