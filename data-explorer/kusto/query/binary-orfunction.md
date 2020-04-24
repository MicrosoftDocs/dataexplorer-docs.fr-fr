---
title: binary_or() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit binary_or() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f6d68137ded3b164ca25d82e0c17b6fef6fc1a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517593"
---
# <a name="binary_or"></a>binary_or()

Retourne le résultat du `or` fonctionnement bitwise des deux valeurs. 

```kusto
binary_or(x,y)
```

**Syntaxe**

`binary_or(`*num1* `,` *num2*`)`

**Arguments**

* *num1*, *num2*: de longs nombres.

**Retourne**

Retourne l’opération de l’OR logique sur une paire de numéros : num1 num2.