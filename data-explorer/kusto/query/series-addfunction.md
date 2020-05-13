---
title: series_add ()-Azure Explorateur de données
description: Cet article décrit series_add () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b5847fec10eb76a6fe5a139809766d2a3ca4f089
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372922"
---
# <a name="series_add"></a>series_add()

Calcule l’ajout au niveau des éléments de deux entrées de série numérique.

**Syntaxe**

`series_add(`*Series1* `,` *Series2*`)`

**Arguments**

* *Series1, Series2*: entrez des tableaux numériques à ajouter au niveau des éléments dans un résultat de tableau dynamique. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Tableau dynamique des opérations d’ajout d’éléments calculés entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit une `null` valeur d’élément.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[5, 4, 5]|
|[2, 4, 8]|[8, 4, 2]|[10, 8, 10]|
|[3, 6, 12]|[12, 6, 3]|[15, 12, 15]|
