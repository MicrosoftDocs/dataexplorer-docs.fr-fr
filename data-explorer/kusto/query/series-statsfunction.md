---
title: series_stats ()-Azure Explorateur de données
description: Cet article décrit series_stats () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 5eb6bb2d11b35a7844a0366fc10797db621f6120
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241970"
---
# <a name="series_stats"></a>series_stats()

`series_stats()` retourne des statistiques pour une série dans plusieurs colonnes.  

La `series_stats()` fonction prend une colonne contenant un tableau numérique dynamique comme entrée et calcule les colonnes suivantes :
* `min`: valeur minimale dans le tableau d’entrée
* `min_idx`: première position de la valeur minimale dans le tableau d’entrée
* `max`: valeur maximale dans le tableau d’entrée
* `max_idx`: première position de la valeur maximale dans le tableau d’entrée
* `avg`: valeur moyenne du tableau d’entrée
* `variance`: exemple de variance du tableau d’entrée
* `stdev`: exemple d’écart type du tableau d’entrée

> [!NOTE] 
> Cette fonction retourne plusieurs colonnes et ne peut donc pas être utilisée comme argument pour une autre fonction.

## <a name="syntax"></a>Syntaxe

Project `series_stats(` *x* `[,` *ignore_nonfinite* `])` ou Extend `series_stats(` *x* `)` retourne toutes les colonnes mentionnées ci-dessus avec les noms suivants : series_stats_x_min, series_stats_x_min_idx, etc.
 
Project (m, mi) = `series_stats(` *x* `)` ou extend (m, mi) = `series_stats(` *x* `)` retourne les colonnes suivantes : m (min) et mi (min_idx).

## <a name="arguments"></a>Arguments

* *x*: cellule de tableau dynamique, qui est un tableau de valeurs numériques. 
* *ignore_nonfinite*: indicateur booléen (facultatif, par défaut : `false` ) qui spécifie s’il faut calculer les statistiques tout en ignorant les valeurs non finies (*null*, *Nan*, *INF*, etc.). Si la valeur est `false` , les valeurs retournées seraient `null` si des valeurs non finies sont présentes dans le tableau.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32,8|28.5036338535483|812.457142857143|
