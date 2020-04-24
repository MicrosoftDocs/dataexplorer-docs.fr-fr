---
title: series_add() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_add() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ea8c391060e487413a166c236abf789edcba0a0c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509059"
---
# <a name="series_add"></a>series_add()

Calcule l’ajout élément-sage de deux entrées de série numérique.

**Syntaxe**

`series_add(`*série1* `,` *série2*`)`

**Arguments**

* *série1, série2*: Entrées de tableaux numériques pour être élément-sage ajouté dans un résultat de tableau dynamique. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Gamme dynamique de module calculé élément-sage ajouter l’opération entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de `null` différentes tailles) donne une valeur d’élément.

**Exemple**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[5,4,5]|
|[2,4,8]|[8,4,2]|[10,8,10]|
|[3,6,12]|[12,6,3]|[15,12,15]|