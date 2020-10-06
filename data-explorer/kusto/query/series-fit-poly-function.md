---
title: series_fit_poly ()-Azure Explorateur de données
description: Cet article décrit les series_fit_poly () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/21/2020
ms.openlocfilehash: 68f9f55b1ff0c66bb5d50e624f9063d7e177f50a
ms.sourcegitcommit: d0f8d71261f8f01e7676abc77283f87fc450c7b1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771788"
---
# <a name="series_fit_poly"></a>series_fit_poly ()

Applique une régression polynomiale à partir d’une variable indépendante (x_series) à une variable dépendante (y_series). Cette fonction prend une table contenant plusieurs séries (tableaux numériques dynamiques) et génère le plus grand degré polynomial pour chaque série à l’aide de la [régression polynomiale](https://en.wikipedia.org/wiki/Polynomial_regression). 

> [!TIP]
> * Pour la régression linéaire d’une série espacée uniformément, telle qu’elle est créée par l' [opérateur make-Series](make-seriesoperator.md), utilisez la fonction plus simple [series_fit_line ()](series-fit-linefunction.md). Voir l' [exemple 2](#example-2).
> * Si *x_series* est fourni et que la régression est effectuée pour un degré élevé, envisagez de normaliser la plage [0-1]. Voir l' [exemple 3](#example-3).
> * Si *x_series* est de type DateTime, il doit être converti en type double et normalisé. Voir l' [exemple 3](#example-3).
> * Pour une implémentation de référence de la régression polynomiale à l’aide de Python Inline, consultez [series_fit_poly_fl ()](../functions-library/series-fit-poly-fl.md).


## <a name="syntax"></a>Syntaxe

`T | extend  series_fit_poly(`*y_series* `, ` *x_series* `, ` *degré*`)`
  
## <a name="arguments"></a>Arguments

|Argument| Description| Obligatoire ou facultatif| Notes|
|---|---|---|---|
| *y_series* | Tableau numérique dynamique contenant la [variable dépendante](https://en.wikipedia.org/wiki/Dependent_and_independent_variables). | Obligatoire |
| *x_series* | Tableau numérique dynamique contenant la [variable indépendante](https://en.wikipedia.org/wiki/Dependent_and_independent_variables). | Facultatif. Obligatoire uniquement pour les [séries espacées](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series)de manière inégale. | Si ce paramètre n’est pas spécifié, sa valeur par défaut est [1, 2,..., longueur (y_series)].|
| *globale* | Ordre requis du polynôme à ajuster. Par exemple, 1 pour la régression linéaire, 2 pour la régression quadratique, et ainsi de suite. | Facultatif | La valeur par défaut est 1 (régression linéaire).|

## <a name="returns"></a>Retours

La `series_fit_poly()` fonction retourne les colonnes suivantes :

* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) est une mesure standard de la qualité adaptée. La valeur est un nombre compris dans la plage [0-1], où 1-est le meilleur ajustement possible, et 0 signifie que les données ne sont pas ordonnées et ne correspondent à aucune ligne.
* `coefficients`: Tableau numérique contenant les coefficients du polynôme le mieux adapté au degré donné, classé du coefficient de puissance le plus élevé au plus bas.
* `variance`: Variance de la variable dépendante (y_series).
* `rvariance`: Variance résiduelle qui est l’écart entre les valeurs des données d’entrée et les valeurs approximatives.
* `poly_fit`: Tableau numérique contenant une série de valeurs du polynôme le mieux ajusté. La longueur de la série est égale à la longueur de la variable dépendante (y_series). Valeur utilisée pour la représentation graphique.

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1

Un cinquième ordre polynomial avec un bruit sur les axes x & y :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 200 step 1
| project x = rand()*5 - 2.3
| extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
| extend y = y + (rand() - 0.5)*0.5*y
| summarize x=make_list(x), y=make_list(y)
| extend series_fit_poly(y, x, 5)
| project-rename fy=series_fit_poly_y_poly_fit, coeff=series_fit_poly_y_coefficients
|fork (project x, y, fy) (project-away x, y, fy)
| render linechart 
```

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-1.png" alt-text="Graphique présentant le cinquième ordre polynomial pour une série avec du bruit":::

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-table-1.png" alt-text="Graphique présentant le cinquième ordre polynomial pour une série avec du bruit" border="false":::

### <a name="example-2"></a>Exemple 2

Vérifiez que le `series_fit_poly` degré = 1 correspond `series_fit_line` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_series1
| extend series_fit_line(y)
| extend series_fit_poly(y)
| project-rename y_line = series_fit_line_y_line_fit, y_poly = series_fit_poly_y_poly_fit
| fork (project x, y, y_line, y_poly) (project-away id, x, y, y_line, y_poly) 
| render linechart with(xcolumn=x, ycolumns=y, y_line, y_poly)
```

:::image type="content" source="images/series-fit-poly-function/fit-poly-line.png" alt-text="Graphique présentant le cinquième ordre polynomial pour une série avec du bruit":::

:::image type="content" source="images/series-fit-poly-function/fit-poly-line-table.png" alt-text="Graphique présentant le cinquième ordre polynomial pour une série avec du bruit" border="false":::
    
### <a name="example-3"></a>Exemple 3

Séries chronologiques irrégulières (espacées de manière inégale) :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  x-axis must be normalized to the range [0-1] if either degree is relatively big (>= 5) or original x range is big.
//  so if x is a time axis it must be normalized as conversion of timestamp to long generate huge numbers (number of 100 nano-sec ticks from 1/1/1970)
//
//  Normalization: x_norm = (x - min(x))/(max(x) - min(x))
//
irregular_ts
| extend series_stats(series_add(TimeStamp, 0))                                                                 //  extract min/max of time axis as doubles
| extend x = series_divide(series_subtract(TimeStamp, series_stats__min), series_stats__max-series_stats__min)  // normalize time axis to [0-1] range
| extend series_fit_poly(num, x, 8)
| project-rename fnum=series_fit_poly_num_poly_fit
| render timechart with(ycolumns=num, fnum)
```
:::image type="content" source="images/series-fit-poly-function/irregular-time-series-1.png" alt-text="Graphique présentant le cinquième ordre polynomial pour une série avec du bruit":::
