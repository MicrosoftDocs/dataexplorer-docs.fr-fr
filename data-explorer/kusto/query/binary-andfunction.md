---
title: binary_and ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit binary_and () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 93318785cff98c7ca024b638e5e90f58cd9cd9d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249356"
---
# <a name="binary_and"></a>binary_and()

Retourne un résultat de l’opération au niveau du bit `and` entre deux valeurs.

```kusto
binary_and(x,y) 
```

## <a name="syntax"></a>Syntaxe

`binary_and(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Arguments

* *num1*, *num2*: nombres longs.

## <a name="returns"></a>Retours

Retourne une opération AND logique sur une paire de nombres : num1 & num2.