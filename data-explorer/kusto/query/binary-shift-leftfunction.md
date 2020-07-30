---
title: binary_shift_left ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit binary_shift_left () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 46d18bb5d1f661c5346f5ff825c9597088d3f5cf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349038"
---
# <a name="binary_shift_left"></a>binary_shift_left()

Retourne l’opération de décalage binaire vers la gauche sur une paire de nombres.

```kusto
binary_shift_left(x,y)  
```

## <a name="syntax"></a>Syntaxe

`binary_shift_left(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Arguments

* *num1*, *num2*: int Numbers.

## <a name="returns"></a>Retourne

Retourne l’opération de décalage binaire vers la gauche sur une paire de nombres : num1 <<  (num2% 64).
Si n est négatif, une valeur NULL est retournée.