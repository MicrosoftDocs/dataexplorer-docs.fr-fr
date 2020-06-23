---
title: ToTimeSpan ()-Azure Explorateur de données
description: Cet article décrit ToTimeSpan () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b785f346dd95a9c6a8cb9d6148e889c42ac4b02c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129037"
---
# <a name="totimespan"></a>totimespan()

Convertit l’entrée en scalaire [TimeSpan](./scalar-data-types/timespan.md) .

```kusto
totimespan("0.00:01:00") == time(1min)
```

**Syntaxe**

`totimespan(Expr)`

**Arguments**

* *`Expr`*: Expression qui sera convertie en [TimeSpan](./scalar-data-types/timespan.md).

**Retourne**

Si la conversion réussit, le résultat sera une valeur [TimeSpan](./scalar-data-types/timespan.md) .
Sinon, le résultat sera null.
