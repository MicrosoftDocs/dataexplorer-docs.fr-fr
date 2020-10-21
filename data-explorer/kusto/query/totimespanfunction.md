---
title: ToTimeSpan ()-Azure Explorateur de données
description: Cet article décrit ToTimeSpan () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0779a6260cc87f8a602f4751d28c33de9bacb57a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243749"
---
# <a name="totimespan"></a>totimespan()

Convertit l’entrée en scalaire [TimeSpan](./scalar-data-types/timespan.md) .

```kusto
totimespan("0.00:01:00") == time(1min)
```

## <a name="syntax"></a>Syntaxe

`totimespan(Expr)`

## <a name="arguments"></a>Arguments

* *`Expr`*: Expression qui sera convertie en [TimeSpan](./scalar-data-types/timespan.md).

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat sera une valeur [TimeSpan](./scalar-data-types/timespan.md) .
Sinon, le résultat sera null.
