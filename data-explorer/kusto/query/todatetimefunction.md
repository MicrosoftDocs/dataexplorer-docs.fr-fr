---
title: ToDateTime ()-Azure Explorateur de données
description: Cet article décrit ToDateTime () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f5c108b670534728f34db8975f16d713848dd8f4
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264606"
---
# <a name="todatetime"></a>todatetime()

Convertit l’entrée en valeur scalaire [DateTime](./scalar-data-types/datetime.md) .

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**Syntaxe**

`todatetime(`*Expr*`)`

**Arguments**

* *Expr*: expression qui sera convertie en [DateTime](./scalar-data-types/datetime.md).

**Retourne**

Si la conversion réussit, le résultat sera une valeur [DateTime](./scalar-data-types/datetime.md) .
Sinon, le résultat sera null.
 
> [!NOTE]
> Préférez l’utilisation [de DateTime ()](./scalar-data-types/datetime.md) dans la mesure du possible.
