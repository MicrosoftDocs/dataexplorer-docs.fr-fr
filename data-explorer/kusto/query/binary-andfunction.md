---
title: binary_and() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit binary_and() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c5501218c1ddb69a73f685fda78f3b3482afb5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517678"
---
# <a name="binary_and"></a>binary_and()

Retourne le résultat de `and` l’opération bitwise entre deux valeurs.

```kusto
binary_and(x,y) 
```

**Syntaxe**

`binary_and(`*num1* `,` *num2*`)`

**Arguments**

* *num1*, *num2*: de longs nombres.

**Retourne**

Retourne logique ET opération sur une paire de numéros: num1 & num2.