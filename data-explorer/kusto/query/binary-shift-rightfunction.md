---
title: binary_shift_right() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit binary_shift_right() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94c8c695f1c5d16ee0a7e3a92882486b8a8ef5d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517542"
---
# <a name="binary_shift_right"></a>binary_shift_right()

Retourne l’opération binaire de décalage droit sur une paire de nombres.

```kusto
binary_shift_right(x,y) 
```

**Syntaxe**

`binary_shift_right(`*num1* `,` *num2*`)`

**Arguments**

* *num1*, *num2*: de longs nombres.

**Retourne**

Retourne le changement binaire à droite sur une paire de numéros : num1 >>  (num2%64).
Si n est négatif, une valeur NULL est retournée.