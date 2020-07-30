---
title: binary_and ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit binary_and () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 611580aebbfd974377f5a22ec904bbbcdbeb6e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349089"
---
# <a name="binary_and"></a>binary_and()

Retourne un résultat de l’opération au niveau du bit `and` entre deux valeurs.

```kusto
binary_and(x,y) 
```

## <a name="syntax"></a>Syntaxe

`binary_and(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Arguments

* *num1*, *num2*: nombres longs.

## <a name="returns"></a>Retourne

Retourne une opération AND logique sur une paire de nombres : num1 & num2.