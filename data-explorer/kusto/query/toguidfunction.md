---
title: toguid() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit toguid () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb1df5fe91b75e5d3b7d1a9f40b8b3079cac9944
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506135"
---
# <a name="toguid"></a>toguid()

Convertit l’entrée en [`guid`](./scalar-data-types/guid.md) représentation.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

**Syntaxe**

`toguid(`*Expr*`)`

**Arguments**

* *Expr*: Expression qui sera [`guid`](./scalar-data-types/guid.md) convertie en scalar. 

**Retourne**

Si la conversion est réussie, le résultat sera un [`guid`](./scalar-data-types/guid.md) scalar.
Si la conversion n’est `null`pas réussie, le résultat sera .

*Remarque*: Préférez utiliser [le guid()](./scalar-data-types/guid.md) lorsque c’est possible.