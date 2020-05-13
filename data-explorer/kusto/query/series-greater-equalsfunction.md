---
title: series_greater_equals ()-Azure Explorateur de données
description: Cet article décrit series_greater_equals () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 6c394c33af25e1891131c7ca2a47359f3cdcd059
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372655"
---
# <a name="series_greater_equals"></a>series_greater_equals()

Calcule l’opération logique supérieure ou égale à l’élément ( `>=` ) de deux entrées de série numérique.

**Syntaxe**

`series_greater_equals (`*Series1* `,` *Series2*`)`

**Arguments**

* *Series1, Series2*: les tableaux numériques d’entrée doivent être comparés par élément. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Tableau dynamique de valeurs booléennes contenant l’opération logique supérieure ou égale à l’élément calculé entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit une `null` valeur d’élément.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_equals_s2 = series_greater_equals(s1, s2)
```

|s1|s2|s1_greater_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[false, true, true]|

**Voir aussi**

Pour les comparaisons de statistiques de série entières, consultez :
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
