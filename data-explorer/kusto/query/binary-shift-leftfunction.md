---
title: binary_shift_left() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit binary_shift_left() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 15e2bee789e627709ccfedde8eccead7f2578b51
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517559"
---
# <a name="binary_shift_left"></a>binary_shift_left()

Retourne le décalage binaire à gauche sur une paire de nombres.

```kusto
binary_shift_left(x,y)  
```

**Syntaxe**

`binary_shift_left(`*num1* `,` *num2*`)`

**Arguments**

* *num1*, *num2*: numéros int.

**Retourne**

Retourne le décalage binaire à gauche sur une paire de numéros : num1 <<  (num2%64).
Si n est négatif, une valeur NULL est retournée.