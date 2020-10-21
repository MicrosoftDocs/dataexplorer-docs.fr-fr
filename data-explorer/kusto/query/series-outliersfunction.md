---
title: series_outliers ()-Azure Explorateur de données
description: Cet article décrit series_outliers () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: b3ee62940e70f08fa7afb3abf011ee02bda02c7a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246012"
---
# <a name="series_outliers"></a>series_outliers()

Notation des points d’anomalies dans une série.

La fonction prend une expression avec un tableau numérique dynamique comme entrée et génère un tableau numérique dynamique de même longueur. Chaque valeur du tableau indique le score d’une anomalie possible, à l’aide de [« test de Tukey »](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences). Une valeur supérieure à 1,5 dans le même élément de l’entrée indique une anomalie de hausse ou de refus. Une valeur inférieure à-1,5 indique une anomalie de refus.

## <a name="syntax"></a>Syntaxe

`series_outliers(`*x* `, ` *genre* `, ` *ignore_val* `, ` *min_percentile* `, ` *max_percentile*`)`

## <a name="arguments"></a>Arguments

* *x*: cellule de tableau dynamique qui est un tableau de valeurs numériques
* *genre*: algorithme de détection des valeurs hors norme. Prend actuellement en charge `"tukey"` (« Tukey » traditionnel) et  `"ctukey"` (personnalisé « Tukey »). La valeur par défaut est `"ctukey"`
* *ignore_val*: valeur numérique indiquant les valeurs manquantes dans la série. La valeur par défaut est double (null). Le score des valeurs NULL et ignore est défini sur `0`
* *min_percentile*: pour le calcul de la plage inter-quantile normale. La valeur par défaut est 10, les valeurs personnalisées prises en charge sont comprises dans la plage `[2.0, 98.0]` ( `ctukey` uniquement)
* *max_percentile*: identique, la valeur par défaut est 90, les valeurs personnalisées prises en charge sont comprises dans la plage `[2.0, 98.0]` (ctukey uniquement)

Le tableau suivant décrit les différences entre `"tukey"` et `"ctukey"` :

| Algorithm | Plage de quantiles par défaut | Prend en charge de la plage de quantiles personnalisée |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25% / 75%              | Non                             |
| `"ctukey"`| 10% / 90%              | Oui                            |

> [!TIP]
> La meilleure façon d’utiliser cette fonction consiste à l’appliquer aux résultats de l’opérateur [Make-Series](make-seriesoperator.md) .

## <a name="example"></a>Exemple

Une série chronologique avec un bruit crée des valeurs hors norme. Si vous souhaitez remplacer ces valeurs hors norme (bruit) par la valeur moyenne, utilisez series_outliers () pour détecter les valeurs hors norme, puis remplacez-les.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" source="images/series-outliersfunction/series-outliers.png" alt-text="Valeurs hors norme de la série" border="false":::
