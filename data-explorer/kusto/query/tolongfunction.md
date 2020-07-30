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
ms.openlocfilehash: fb209ff3784f6e24f184b576a1e8f94c52834ba4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340953"
---
# <a name="tolong"></a>tolong()

Convertit l’entrée en une représentation de nombre longue (signée 64 bits).

```kusto
tolong("123") == 123
```

## <a name="syntax"></a>Syntaxe

`tolong(`*Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera convertie en long. 

## <a name="returns"></a>Retourne

Si la conversion réussit, le résultat est un nombre long.
Si la conversion échoue, le résultat sera `null` .
 
*Remarque*: préférez utiliser [long ()](./scalar-data-types/long.md) lorsque cela est possible.