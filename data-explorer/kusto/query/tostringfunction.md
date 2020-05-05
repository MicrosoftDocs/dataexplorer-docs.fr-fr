---
title: ToString ()-Azure Explorateur de données
description: Cet article décrit ToString () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 634f54533e83575139d8399124cc068af56d8574
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741667"
---
# <a name="tostring"></a>tostring()

Convertit l’entrée en une représentation sous forme de chaîne.

```kusto
tostring(123) == "123"
```

**Syntaxe**

`tostring(`*`Expr`*`)`

**Arguments**

* *`Expr`*: Expression qui sera convertie en chaîne. 

**Retourne**

Si la *`Expr`* valeur n’est pas null, le résultat est une représentation sous forme de *`Expr`* chaîne de.
Si la *`Expr`* valeur est null, le résultat est une chaîne vide.
 