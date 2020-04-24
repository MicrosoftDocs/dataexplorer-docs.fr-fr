---
title: binary_xor() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit binary_xor() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2f487aa44f8885cbb443c31b8bb3a503e1a76fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517525"
---
# <a name="binary_xor"></a>binary_xor()

Retourne le résultat du `xor` fonctionnement bitwise des deux valeurs.

```kusto
binary_xor(x,y)
```

**Syntaxe**

`binary_xor(`*num1* `,` *num2*`)`

**Arguments**

* *num1*, *num2*: de longs nombres.

**Retourne**

Retourne l’opération logique XOR sur une paire de numéros: num1 num2.