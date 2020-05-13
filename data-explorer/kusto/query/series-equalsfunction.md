---
title: series_equals ()-Azure Explorateur de données
description: Cet article décrit series_equals () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 85c6df85814d4b4aa4e1b00786d3d206d02116cd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372823"
---
# <a name="series_equals"></a>series_equals()

Calcule l’opération logique d’égalité au niveau `==` des éléments () de deux entrées de série numérique.

**Syntaxe**

`series_equals (`*Series1* `,` *Series2*`)`

**Arguments**

* *Series1, Series2*: les tableaux numériques d’entrée doivent être comparés par élément. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Tableau dynamique de valeurs booléennes contenant l’opération logique d’égalité des éléments calculée entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit une `null` valeur d’élément.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_equals_s2 = series_equals(s1, s2)
```

|s1|s2|s1_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[false, true, false]|

**Voir aussi**

Pour les comparaisons de statistiques de série entières, consultez :
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
