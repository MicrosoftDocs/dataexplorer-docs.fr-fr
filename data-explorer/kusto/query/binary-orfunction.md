---
title: binary_or ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit binary_or () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 97144b244fb6fea5ac218f6160d8aa9e95f50aa4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247888"
---
# <a name="binary_or"></a>binary_or()

Retourne un résultat de l' `or` opération de bits des deux valeurs. 

```kusto
binary_or(x,y)
```

## <a name="syntax"></a>Syntaxe

`binary_or(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Arguments

* *num1*, *num2*: nombres longs.

## <a name="returns"></a>Retours

Retourne une opération OR logique sur une paire de nombres : num1 | num2.