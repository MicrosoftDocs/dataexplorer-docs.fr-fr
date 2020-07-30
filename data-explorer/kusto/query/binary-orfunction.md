---
title: binary_or ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit binary_or () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6903ee36e29551e7af6d08e686c1189e0c0125f3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349055"
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

## <a name="returns"></a>Retourne

Retourne une opération OR logique sur une paire de nombres : num1 | num2.