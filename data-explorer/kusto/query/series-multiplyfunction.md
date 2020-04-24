---
title: series_multiply() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_multiply() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: fa000d1058730e0232790e7f0e3976fa203519c0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508209"
---
# <a name="series_multiply"></a>series_multiply()

Calcule la multiplication élément-sage de deux entrées de série numérique.

**Syntaxe**

`series_multiply(`*série1* `,` *série2*`)`

**Arguments**

* *série1, série2*: Entrées de tableaux numériques, à intégrer élément-sage multiplié dans un résultat de tableau dynamique. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Gamme dynamique de l’opération calculée de multiplication élément-sage entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de `null` différentes tailles) donne une valeur d’élément.

**Exemple**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1,2,4]    |[4,2,1]|   [4,4,4]|
|[2,4,8]    |[8,4,2]|   [16,16,16]|
|[3,6,12]   |[12,6,3]|  [36,36,36]|