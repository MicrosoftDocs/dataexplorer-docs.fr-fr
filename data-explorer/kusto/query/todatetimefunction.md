---
title: todatetime() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit todatetime() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abe3852195b1a79ab5c86176698099ed6e7ff7af
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506339"
---
# <a name="todatetime"></a>todatetime()

Convertit l’entrée en scalar [datetime.](./scalar-data-types/datetime.md)

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**Syntaxe**

`todatetime(`*Expr*`)`

**Arguments**

* *Expr*: Expression qui sera convertie à [ce jour](./scalar-data-types/datetime.md). 

**Retourne**

Si la conversion est réussie, le résultat sera une valeur [datetime.](./scalar-data-types/datetime.md)
Si la conversion n’est pas réussie, le résultat sera nul.
 
*Remarque*: Préférez utiliser [l’heure de la date()](./scalar-data-types/datetime.md) lorsque c’est possible.