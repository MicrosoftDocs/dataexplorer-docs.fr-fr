---
title: series_subtract ()-Azure Explorateur de données
description: Cet article décrit series_subtract () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 388f24f12993bcdc91d86bfc3f3f20967e0b1cc5
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372418"
---
# <a name="series_subtract"></a>series_subtract()

Calcule la soustraction au niveau des éléments de deux entrées de série numérique.

**Syntaxe**

`series_subtract(`*Series1* `,` *Series2*`)`

**Arguments**

* *Series1, Series2*: valeurs numériques d’entrée, le deuxième élément est soustrait de la première à un résultat de tableau dynamique. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Tableau dynamique d’opérations de soustraction d’éléments calculées entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit une `null` valeur d’élément.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[-3, 0, 3]|
|[2, 4, 8]|[8, 4, 2]|[-6, 0,6]|
|[3, 6, 12]|[12, 6, 3]|[-9, 0,9]|
