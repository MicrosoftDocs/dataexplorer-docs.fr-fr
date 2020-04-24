---
title: series_divide() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_divide() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8e8b806c325da9bfce5f79ce5a5c4e5cfadaa838
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508838"
---
# <a name="series_divide"></a>series_divide()

Calcule la division élément-sage de deux entrées de série numérique.

**Syntaxe**

`series_divide(`*série1* `,` *série2*`)`

**Arguments**

* *série1, série2*: Entrées de tableaux numériques, les premiers à être par élément-sage divisé par le second dans un résultat dynamique de tableau. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Gamme dynamique de fonctionnement calculé de division élément-sage entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de `null` différentes tailles) donne une valeur d’élément.

Remarque : la série de résultats est de double type, même si les entrées sont des intégrants. Division par zéro suit la double division de zéro (p. ex. 2/0 donne le double).

**Exemple**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1,2,4]    |[4,2,1]|   [0.25,1.0,4.0]|
|[2,4,8]    |[8,4,2]|   [0.25,1.0,4.0]|
|[3,6,12]   |[12,6,3]|  [0.25,1.0,4.0]|