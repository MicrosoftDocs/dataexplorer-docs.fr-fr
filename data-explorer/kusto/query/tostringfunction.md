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
ms.openlocfilehash: 2093ff1117cf7744af550cf93c3fe630fa40a6e6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340172"
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

## <a name="returns"></a>Retourne

Si la *`Expr`* valeur n’est pas null, le résultat est une représentation sous forme de chaîne de *`Expr`* .
Si la *`Expr`* valeur est null, le résultat est une chaîne vide.
 