---
title: tobool() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit tobool() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3e1867c99ae241b3f7e09ab8ee873d5ae5d374e0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506356"
---
# <a name="tobool"></a>tobool()

Convertit l’entrée en représentation boolean (signé 8 bits).

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**Syntaxe**

`tobool(`*Expr*`)`
`)` Expr (alias)*Expr* `toboolean(`

**Arguments**

* *Expr*: Expression qui sera convertie en boolean. 

**Retourne**

Si la conversion est réussie, le résultat sera un boolean.
Si la conversion n’est `null`pas réussie, le résultat sera .
 