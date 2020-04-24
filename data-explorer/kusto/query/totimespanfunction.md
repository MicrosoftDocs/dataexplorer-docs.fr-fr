---
title: totimespan() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit totimespan() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 504d4a74e8c1b58a8a97fd80d6c846fcf7e3f527
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505863"
---
# <a name="totimespan"></a>totimespan()

Convertit l’entrée en scalar de [timespan.](./scalar-data-types/timespan.md)

```kusto
totimespan("0.00:01:00") == time(1min)
```

**Syntaxe**

`totimespan(`*Expr*`)`

**Arguments**

* *Expr*: Expression qui sera convertie en [timespan](./scalar-data-types/timespan.md). 

**Retourne**

Si la conversion est réussie, le résultat sera une valeur [de temps.](./scalar-data-types/timespan.md)
Si la conversion n’est pas réussie, le résultat sera nul.
 