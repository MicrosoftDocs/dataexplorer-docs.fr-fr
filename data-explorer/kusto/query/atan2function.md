---
title: atan2() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit atan2() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8b49191c9d955cf5a91bde2032798f4703f7910
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518392"
---
# <a name="atan2"></a>atan2()

Calcule l’angle, en radians, entre l’axe X positif et le rayon de l’origine jusqu’au point (y, x).

**Syntaxe**

`atan2(`*y*`,`*x*`)`

**Arguments**

* *x*: X coordonnées (un vrai nombre).
* *y*: Y coordonnées (un nombre réel).

**Retourne**

* L’angle, en radians, entre l’axe X positif et le rayon de l’origine au point (y, x).

**Exemples**

```kusto
print atan2_0 = atan2(1,1) // Pi / 4 radians (45 degrees)
| extend atan2_1 = atan2(0,-1) // Pi radians (180 degrees)
| extend atan2_2 = atan2(-1,0) // - Pi / 2 radians (-90 degrees)
```

|atan2_0|atan2_1|atan2_2|
|---|---|---|
|0.785398163397448|3.14159265358979|-1.5707963267949|