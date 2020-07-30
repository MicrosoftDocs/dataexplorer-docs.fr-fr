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
ms.openlocfilehash: 2daea4d190ed349c41a8eecf2eef53b2c2b93716
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350687"
---
# <a name="toint"></a>toint()

Convertit l’entrée en représentation sous forme de nombre entier (32 bits).

```kusto
toint("123") == int(123)
```

## <a name="syntax"></a>Syntaxe

`toint(`*Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera convertie en entier. 

## <a name="returns"></a>Retourne

Si la conversion réussit, le résultat sera un entier.
Si la conversion échoue, le résultat sera `null` .
 
*Remarque*: préférez l’utilisation [de int ()](./scalar-data-types/int.md) dans la mesure du possible.
