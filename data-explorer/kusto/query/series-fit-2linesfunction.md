---
title: series_fit_2lines() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_fit_2lines() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 05163735c88c3229c13d76e3f8fde9bff091b239
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663504"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

Applique deux segments de régression linéaire sur une série, retournant plusieurs colonnes.  

Prend une expression contenant un tableau numérique dynamique comme entrée et applique [deux segments de régression linéaire](https://en.wikipedia.org/wiki/Segmented_regression) afin d’identifier et de quantifier le changement de tendance dans une série. La fonction s’adapte sur les index de la série et dans chaque itération divise la série à 2 parties, s’adapte à une ligne séparée (en utilisant [series_fit_line))](series-fit-linefunction.md)) à chaque partie et calcule le r-carré total. La meilleure séparation est celle qui optimise la valeur r-square ; la fonction renvoie ses paramètres :
* `rsquare`: [r-square](https://en.wikipedia.org/wiki/Coefficient_of_determination) est une mesure standard de la qualité de l’ajustement. Il s’agit d’un nombre dans la plage [0-1], où 1 est la meilleure correspondance possible, et 0 signifie que les données sont totalement désordonnées et ne correspondent à aucune ligne
* `split_idx`: l’indice du point de rupture à 2 segments (zéro à base)
* `variance`: variance des données d’entrée
* `rvariance`: écart résiduel qui est la variance entre les données d’entrée valorise les approximées (par les 2 segments de ligne).
* `line_fit`: tableau numérique tenant une série de valeurs de la ligne la mieux ajustée. La longueur de la série est égale à la longueur du tableau d’entrée. Elle est principalement utilisée pour les graphiques.
* `right_rsquare`: r-carré de la ligne sur le côté droit de la scission, voir [series_fit_line()](series-fit-linefunction.md)
* `right_slope`: pente de la ligne approximative droite (c’est un de y’ax’b)
* `right_interception`: interception de la ligne gauche approximative (c’est b de y’ax-b)
* `right_variance`: variance des données d’entrée sur le côté droit de la scission
* `right_rvariance`: écart résiduel des données d’entrée sur le côté droit de la scission
* `left_rsquare`: r-carré de la ligne sur le côté gauche de la scission, voir [series_fit_line()](series-fit-linefunction.md)
* `left_slope`: pente de la ligne approximative gauche (c’est un de y’ax’b)
* `left_interception`: interception de la ligne gauche approximative (c’est b de y’ax-b)
* `left_variance`: variance des données d’entrée sur le côté gauche de la scission
* `left_rvariance`: écart résiduel des données d’entrée sur le côté gauche de la scission

*Notez* que cette fonction renvoie plusieurs colonnes donc elle ne peut pas être utilisée comme argument pour une autre fonction.

**Syntaxe**

projet `series_fit_2lines(` *x*`)`
* Reviendra toutes les colonnes mentionnées ci-dessus avec les noms suivants : series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx et etc.
projet (rs, si,`series_fit_2lines(`v)md*x*`)`
* Retournera les colonnes suivantes: rs (r-square), si (indice fractionné), v (variance) et le reste ressemblera à series_fit_2lines_x_rvariance,`series_fit_2lines(`series_fit_2lines_x_line_fit et etc. étendre (rs, si, v)x*x*`)`
* Renvoie uniquement : rs (r-square), si (split index) et v (variance).
  
**Arguments**

* *x*: Gamme dynamique de valeurs numériques.  

> [!TIP]
> La façon la plus pratique d’utiliser cette fonction est de l’appliquer aux résultats de [l’opérateur de la série make.](make-seriesoperator.md)

**Exemples**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/samples/series-fit-2lines.png" alt-text="Série ajustement 2 lignes":::
