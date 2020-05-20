---
title: ToInt (()-Azure Explorateur de données
description: Cet article décrit ToInt (() dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7f0ede908be2689165f641038b2b6f699c0eb543
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550603"
---
# <a name="toint"></a>toint()

Convertit l’entrée en représentation sous forme de nombre entier (32 bits).

```kusto
toint("123") == 123s
```

**Syntaxe**

`toint(`*Expr*`)`

**Arguments**

* *Expr*: expression qui sera convertie en entier. 

**Retourne**

Si la conversion réussit, le résultat sera un entier.
Si la conversion échoue, le résultat sera `null` .
 
*Remarque*: préférez l’utilisation [de int ()](./scalar-data-types/int.md) dans la mesure du possible.