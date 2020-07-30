---
title: ToDecimal (()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ToDecimal (() dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f699508436c9e2533661a440be2ac8f5f8d94688
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350772"
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

## <a name="returns"></a>Retourne

Si la conversion réussit, le résultat sera un nombre décimal.
Si la conversion échoue, le résultat sera `null` .
 
*Remarque*: préférez utiliser [Real ()](./scalar-data-types/real.md) dans la mesure du possible.