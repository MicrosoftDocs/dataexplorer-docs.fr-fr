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
ms.openlocfilehash: e0343ae5cb98e1cb3114e24c963fe2981be82c5b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350789"
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

## <a name="returns"></a>Retourne

Si la conversion réussit, le résultat est une valeur booléenne.
Si la conversion échoue, le résultat sera `null` .
