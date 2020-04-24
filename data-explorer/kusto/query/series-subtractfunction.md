---
title: series_subtract() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_subtract() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1e984bc35192da5d61448211c49ff582f225eb19
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507937"
---
# <a name="series_subtract"></a>series_subtract()

Calcule la soustraction élément-sage de deux entrées de série numérique.

**Syntaxe**

`series_subtract(`*série1* `,` *série2*`)`

**Arguments**

* *série1, série2*: Entrées de tableaux numériques, la seconde à être élément-sage soustrait de la première dans un résultat de tableau dynamique. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Un tableau dynamique de l’opération calculée de soustraction d’élément-sage entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de `null` différentes tailles) donne une valeur d’élément.

**Exemple**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[-3,0,3]|
|[2,4,8]|[8,4,2]|[-6,0,6]|
|[3,6,12]|[12,6,3]|[-9,0,9]|