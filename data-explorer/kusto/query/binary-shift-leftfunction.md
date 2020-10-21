---
title: binary_shift_left ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit binary_shift_left () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f01f0178fa850009f6298446b02c9d4bd913bf8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252608"
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

## <a name="returns"></a>Retours

Retourne l’opération de décalage binaire vers la gauche sur une paire de nombres : num1 <<  (num2% 64).
Si n est négatif, une valeur NULL est retournée.