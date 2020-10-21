---
title: binary_shift_right ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit binary_shift_right () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d33ecb954a7e1e6d0c9c39bdbf057d284affd22b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243512"
---
# <a name="binary_shift_right"></a>binary_shift_right()

Retourne l’opération de décalage binaire vers la droite sur une paire de nombres.

```kusto
binary_shift_right(x,y) 
```

## <a name="syntax"></a>Syntaxe

`binary_shift_right(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Arguments

* *num1*, *num2*: nombres longs.

## <a name="returns"></a>Retours

Retourne l’opération de décalage binaire vers la droite sur une paire de nombres : num1 >>  (num2% 64).
Si n est négatif, une valeur NULL est retournée.