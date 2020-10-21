---
title: binary_xor ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit binary_xor () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aecf005556a9997e63ee3547f4e0b23da236cf5d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243471"
---
# <a name="binary_xor"></a>binary_xor()

Retourne un résultat de l' `xor` opération de bits des deux valeurs.

```kusto
binary_xor(x,y)
```

## <a name="syntax"></a>Syntaxe

`binary_xor(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Arguments

* *num1*, *num2*: nombres longs.

## <a name="returns"></a>Retours

Retourne une opération XOR logique sur une paire de nombres : num1 ^ num2.