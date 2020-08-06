---
title: tolong ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit tolong () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36c0317f046f146d2812b8830d7fe571d5363c59
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804115"
---
# <a name="tolong"></a>tolong()

Convertit l’entrée en une représentation de nombre longue (signée 64 bits).

```kusto
tolong("123") == 123
```

> [!NOTE]
> Préférez l’utilisation [de long ()](./scalar-data-types/long.md) dans la mesure du possible.

## <a name="syntax"></a>Syntaxe

`tolong(`*Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera convertie en long. 

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat est un nombre long.
Si la conversion échoue, le résultat sera `null` .
 