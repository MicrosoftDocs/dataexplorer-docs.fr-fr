---
title: series_stats_dynamic ()-Azure Explorateur de données
description: Cet article décrit series_stats_dynamic () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 87cee5244fb1276733d4cf44d0477cc3351b947c
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372448"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

Retourne des statistiques pour une série dans un objet dynamique.  

La `series_stats_dynamic()` fonction prend une colonne contenant un tableau numérique dynamique comme entrée et génère une valeur dynamique avec le contenu suivant :
* `min`: valeur minimale dans le tableau d’entrée
* `min_idx`: première position de la valeur minimale dans le tableau d’entrée
* `max`: valeur maximale dans le tableau d’entrée
* `max_idx`: première position de la valeur maximale dans le tableau d’entrée
* `avg`: valeur moyenne du tableau d’entrée
* `variance`: exemple de variance du tableau d’entrée
* `stdev`: exemple d’écart type du tableau d’entrée

**Syntaxe**

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite* x`])`

**Arguments**

* *x*: cellule de tableau dynamique qui est un tableau de valeurs numériques. 
* *ignore_nonfinite*: indicateur booléen (facultatif, par défaut : `false` ) qui spécifie s’il faut calculer les statistiques tout en ignorant les valeurs non finies (*null*, *Nan*, *INF*, etc.). Si la valeur `false` est, le résultat retourné est `null` si des valeurs non finies sont présentes dans le tableau.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|{« min » : 2,0, « min_idx » : 8, « Max » : 87,0, « max_idx » : 3, « AVG » : 32,8, « STDEV » : 28.503633853548269, « variance » : 812.45714285714291}
