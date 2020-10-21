---
title: tolong ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit tolong () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf4960ae3bd11697e4e7203438e1a33af6c90672
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92240991"
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
 