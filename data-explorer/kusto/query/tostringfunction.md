---
title: ToString ()-Azure Explorateur de données
description: Cet article décrit ToString () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 43797dcaa5e1a18b6a84f15fc0af89d88d814ff6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243808"
---
# <a name="tostring"></a>tostring()

Convertit l’entrée en une représentation sous forme de chaîne.

```kusto
tostring(123) == "123"
```

## <a name="syntax"></a>Syntaxe

`tostring(`*`Expr`*`)`

## <a name="arguments"></a>Arguments

* *`Expr`*: Expression qui sera convertie en chaîne. 

## <a name="returns"></a>Retours

Si la *`Expr`* valeur n’est pas null, le résultat est une représentation sous forme de chaîne de *`Expr`* .
Si la *`Expr`* valeur est null, le résultat est une chaîne vide.
 