---
title: tolong() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit tolong() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd354a39c048631ab98390c74cb7cd78ee5376b2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506084"
---
# <a name="tolong"></a>tolong()

Convertit l’entrée en représentation longue (signée 64 bits).

```kusto
tolong("123") == 123
```

**Syntaxe**

`tolong(`*Expr*`)`

**Arguments**

* *Expr*: Expression qui sera convertie en long. 

**Retourne**

Si la conversion est réussie, le résultat sera un nombre long.
Si la conversion n’est `null`pas réussie, le résultat sera .
 
*Remarque*: Préférez utiliser [longtemps ()](./scalar-data-types/long.md) lorsque c’est possible.