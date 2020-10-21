---
title: Ceiling ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Ceiling () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b307121e102229edbe62c4d4dd7f910ea2ce8756
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249326"
---
# <a name="ceiling"></a>ceiling()

Calcule le plus petit entier supérieur ou égal à l’expression numérique spécifiée.

## <a name="syntax"></a>Syntaxe

`ceiling(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>Retours

* Plus petit entier supérieur ou égal à l’expression numérique spécifiée. 

## <a name="examples"></a>Exemples

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|