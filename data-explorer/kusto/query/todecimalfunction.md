---
title: ToDecimal (()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ToDecimal (() dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4b9f020a1d37b7279c0712951ed0c2e39e9e428
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250505"
---
# <a name="todecimal"></a>todecimal()

Convertit l’entrée en représentation sous forme de nombre décimal.

```kusto
todecimal("123.45678") == decimal(123.45678)
```

## <a name="syntax"></a>Syntaxe

`todecimal(`*Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera convertie en valeur décimale. 

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat sera un nombre décimal.
Si la conversion échoue, le résultat sera `null` .
 
*Remarque*: préférez utiliser [Real ()](./scalar-data-types/real.md) dans la mesure du possible.