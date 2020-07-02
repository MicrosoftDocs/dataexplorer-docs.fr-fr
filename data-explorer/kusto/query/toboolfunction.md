---
title: ToBool ()-Azure Explorateur de données
description: Cet article décrit ToBool () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f99406d94e1cd64da8605e5000aa99136c2b119a
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763787"
---
# <a name="tobool"></a>tobool()

Convertit l’entrée en représentation booléenne (8 bits signée).

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**Syntaxe**

`tobool(`*Expr* `)` 
 Expr `toboolean(` *Expr* `)` affecté

**Arguments**

* *Expr*: expression qui sera convertie en valeur booléenne. 

**Retourne**

Si la conversion réussit, le résultat est une valeur booléenne.
Si la conversion échoue, le résultat sera `null` .
