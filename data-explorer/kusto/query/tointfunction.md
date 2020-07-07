---
title: ToInt (()-Azure Explorateur de données
description: Cet article décrit ToInt (() dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1efd06b8fa2961528be65b630933aae74614e9a0
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013607"
---
# <a name="toint"></a>toint()

Convertit l’entrée en représentation sous forme de nombre entier (32 bits).

```kusto
toint("123") == int(123)
```

**Syntaxe**

`toint(`*Expr*`)`

**Arguments**

* *Expr*: expression qui sera convertie en entier. 

**Retourne**

Si la conversion réussit, le résultat sera un entier.
Si la conversion échoue, le résultat sera `null` .
 
*Remarque*: préférez l’utilisation [de int ()](./scalar-data-types/int.md) dans la mesure du possible.
