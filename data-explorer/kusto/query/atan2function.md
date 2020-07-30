---
title: atan2 ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit atan2 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 60b500109f140290427a6d1ad3baba8e25849b57
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349446"
---
# <a name="atan2"></a>atan2()

Calcule l’angle, en radians, entre l’axe des abscisses positif et le rayon depuis l’origine jusqu’au point (y, x).

## <a name="syntax"></a>Syntaxe

`atan2(`*y* `,` *x*`)`

## <a name="arguments"></a>Arguments

* *x*: coordonnée x (un nombre réel).
* *y*: coordonnée y (nombre réel).

## <a name="returns"></a>Retourne

* Angle, en radians, entre l’axe des abscisses positif et le rayon de l’origine jusqu’au point (y, x).

## <a name="examples"></a>Exemples

```kusto
print atan2_0 = atan2(1,1) // Pi / 4 radians (45 degrees)
| extend atan2_1 = atan2(0,-1) // Pi radians (180 degrees)
| extend atan2_2 = atan2(-1,0) // - Pi / 2 radians (-90 degrees)
```

|atan2_0|atan2_1|atan2_2|
|---|---|---|
|0.785398163397448|3,14159265358979|-1,5707963267949|