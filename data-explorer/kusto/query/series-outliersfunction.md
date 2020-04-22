---
title: series_outliers() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_outliers() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 47e479ce7fe09b2456405ac3f7daed4721374f32
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663461"
---
# <a name="series_outliers"></a>series_outliers()

Marque des points d’anomalie dans une série.

Prend une expression contenant un tableau numérique dynamique comme entrée et génère un tableau numérique dynamique de la même longueur. Chaque valeur du tableau indique une vingtaine d’anomalies possibles à [l’aide du test de Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test). Une valeur supérieure à 1,5 ou inférieure à -1,5 indique respectivement une anomalie de hausse ou de baisse dans le même élément de l’entrée.   

**Syntaxe**

`series_outliers(`*x*`, `*genre*`, `*ignore_val min_percentile*`, `*min_percentile*`, `*max_percentile*`)`

**Arguments**

* *x*: Cellule de tableau dynamique qui est un éventail de valeurs numériques
* *type*: Algorithme de détection aberrante. Actuellement `"tukey"` prend en charge `"ctukey"` (Tukey traditionnel) et (Tukey personnalisé). La valeur par défaut est `"ctukey"`
* *ignore_val*: valeur numérique indiquant les valeurs manquantes dans la série, le défaut est double (null). Le score des nulls et `0`des valeurs d’ignorance est fixé à .
* *min_percentile*: pour la calulation de la plage inter quantile normale, le défaut `[2.0, 98.0]` `ctukey` est de 10, les valeurs personnalisées prises en charge sont à portée (seulement) 
* *max_percentile*: même, par défaut est de 90, `[2.0, 98.0]` les valeurs personnalisées prises en charge sont à portée (ctukey seulement) 

Le tableau suivant décrit `"tukey"` les `"ctukey"`différences entre et :

| Algorithm | Plage de quantiles par défaut | Prend en charge de la plage de quantiles personnalisée |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25% / 75%              | Non                             |
| `"ctukey"`| 10% / 90%              | Oui                            |


> [!TIP]
> La façon la plus pratique d’utiliser cette fonction est de l’appliquer aux résultats de [l’opérateur de la série make.](make-seriesoperator.md)

**Exemple**

Supposons que vous avez une série de temps avec un peu de bruit qui crée des valeurs aberrantes et que vous souhaitez remplacer ces valeurs aberrantes (bruit) par la valeur moyenne, vous pourriez utiliser series_outliers() pour détecter les valeurs aberrantes, puis les remplacer:

```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" border="false" source="images/samples/series-outliers.png" alt-text="Valeurs aberrantes de série":::
