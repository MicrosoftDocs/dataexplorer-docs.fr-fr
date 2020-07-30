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
ms.openlocfilehash: 1edc5e3ef8c3c2dea65d332e6ceace653cc5c812
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340137"
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
