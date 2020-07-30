---
title: binary_xor ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit binary_xor () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f988fa3d14dab3188bf96825615972995291655
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349004"
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

## <a name="returns"></a>Retourne

Retourne une opération XOR logique sur une paire de nombres : num1 ^ num2.