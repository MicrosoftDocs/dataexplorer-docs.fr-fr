---
title: series_divide ()-Azure Explorateur de données
description: Cet article décrit series_divide () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7d5bdba030687c17c355eb72ce2fc9c358c10ebd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372848"
---
# <a name="series_divide"></a>series_divide()

Calcule la division au niveau des éléments de deux entrées de série numérique.

**Syntaxe**

`series_divide(`*Series1* `,` *Series2*`)`

**Arguments**

* *Series1, Series2*: entrez des tableaux numériques, le premier étant un élément, divisé par le second en un résultat de tableau dynamique. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Tableau dynamique de l’opération de division par élément calculé entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit une `null` valeur d’élément.

Remarque : la série de résultats est de type double, même si les entrées sont des entiers. La division par zéro suit la double division par zéro (par exemple, 2/0 donne double (+ INF)).

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [0.25, 1.0, 4.0]|
|[2, 4, 8]    |[8, 4, 2]|   [0.25, 1.0, 4.0]|
|[3, 6, 12]   |[12, 6, 3]|  [0.25, 1.0, 4.0]|
