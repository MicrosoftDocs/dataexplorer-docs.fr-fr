---
title: series_outliers ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit series_outliers () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 16e82ec68a463b97699f7d02e18c46df65221c7b
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618652"
---
# <a name="series_outliers"></a>series_outliers()

Notation des points d’anomalies dans une série.

Prend une expression contenant un tableau numérique dynamique comme entrée et génère un tableau numérique dynamique de même longueur. Chaque valeur du tableau indique un score d’anomalie possible à l’aide [du test de Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test). Une valeur supérieure à 1,5 ou inférieure à -1,5 indique respectivement une anomalie de hausse ou de baisse dans le même élément de l’entrée.   

**Syntaxe**

`series_outliers(`*x*`, `*kind*`, `*ignore_val*ignore_val`, `*min_percentile*de min_percentile`, `de type x*max_percentile*`)`

**Arguments**

* *x*: cellule de tableau dynamique qui est un tableau de valeurs numériques
* *genre*: algorithme de détection des valeurs hors norme. Prend actuellement `"tukey"` en charge (Tukey traditionnel `"ctukey"` ) et (Tukey personnalisé). La valeur par défaut est `"ctukey"`
* *ignore_val*: valeur numérique indiquant des valeurs manquantes dans la série, la valeur par défaut est double (null). Le score des valeurs NULL et ignore est défini sur `0`.
* *min_percentile*: pour le calcul de la plage quantile normale, la valeur par défaut est 10, les valeurs personnalisées `[2.0, 98.0]` prises`ctukey` en charge sont comprises dans la plage (uniquement) 
* *max_percentile*: identique, la valeur par défaut est 90, les valeurs personnalisées prises en charge sont comprises dans la plage `[2.0, 98.0]` (ctukey uniquement) 

Le tableau suivant décrit les différences `"tukey"` entre `"ctukey"`et :

| Algorithm | Plage de quantiles par défaut | Prend en charge de la plage de quantiles personnalisée |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25% / 75%              | Non                              |
| `"ctukey"`| 10% / 90%              | Oui                            |


> [!TIP]
> La méthode la plus pratique pour utiliser cette fonction consiste à l’appliquer aux résultats de l’opérateur [Make-Series](make-seriesoperator.md) .

**Exemple**

Supposons que vous ayez une série chronologique avec un bruit qui crée des valeurs hors norme et que vous souhaitiez remplacer ces valeurs hors norme (le bruit) par la valeur moyenne, vous pouvez utiliser series_outliers () pour détecter les valeurs hors norme, puis les remplacer :

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
