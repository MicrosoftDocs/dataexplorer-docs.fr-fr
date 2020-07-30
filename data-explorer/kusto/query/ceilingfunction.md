---
title: Ceiling ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Ceiling () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e2a29d28b25d26d582aa49717d5ce5576276f450
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348902"
---
# <a name="ceiling"></a>ceiling()

Calcule le plus petit entier supérieur ou égal à l’expression numérique spécifiée.

## <a name="syntax"></a>Syntaxe

`ceiling(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>Retourne

* Plus petit entier supérieur ou égal à l’expression numérique spécifiée. 

## <a name="examples"></a>Exemples

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|