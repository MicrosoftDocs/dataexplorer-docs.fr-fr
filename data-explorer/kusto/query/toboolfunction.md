---
title: ToBool ()-Azure Explorateur de données
description: Cet article décrit ToBool () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7fa3d12b1dfc1fcbede2f5f47b3841102b72e5a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250582"
---
# <a name="tobool"></a>tobool()

Convertit l’entrée en représentation booléenne (8 bits signée).

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

## <a name="syntax"></a>Syntaxe

`tobool(`*Expr* `)` 
 Expr `toboolean(` *Expr* `)` affecté

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera convertie en valeur booléenne. 

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat est une valeur booléenne.
Si la conversion échoue, le résultat sera `null` .
