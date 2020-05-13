---
title: series_fit_2lines ()-Azure Explorateur de données
description: Cet article décrit series_fit_2lines () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d4b4be37f171439b47399ecfbb314b1a9b704afd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372709"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

Applique deux segments de régression linéaire sur une série, en retournant plusieurs colonnes.  

Prend une expression contenant un tableau numérique dynamique comme entrée et applique [deux segments de régression linéaire](https://en.wikipedia.org/wiki/Segmented_regression) pour identifier et quantifier les modifications de tendance dans une série. La fonction itère sur les index de série et, dans chaque itération, divise la série en 2 parties, ajuste une ligne distincte (à l’aide de [series_fit_line ()](series-fit-linefunction.md)) à chaque partie et calcule le carré r total. La meilleure séparation est celle qui optimise la valeur r-square ; la fonction renvoie ses paramètres :
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) est une mesure standard de la qualité adaptée. Il s’agit d’un nombre dans la plage [0-1], où 1 est la meilleure correspondance possible, et 0 signifie que les données sont totalement désordonnées et ne correspondent à aucune ligne
* `split_idx`: index de point de rupture à 2 segments (de base zéro)
* `variance`: variance des données d’entrée
* `rvariance`: variance résiduelle qui est l’écart entre les valeurs des données d’entrée et les valeurs approximatives (par segments de 2 lignes).
* `line_fit`: tableau numérique contenant une série de valeurs de la ligne la mieux adaptée. La longueur de la série est égale à la longueur du tableau d’entrée. Elle est principalement utilisée pour les graphiques.
* `right_rsquare`: r-carré de la ligne sur le côté droit du fractionnement, consultez [series_fit_line ()](series-fit-linefunction.md)
* `right_slope`: pente de la ligne approximative droite (il s’agit d’un de y = ax + b)
* `right_interception`: interception de la ligne gauche approximative (il s’agit de b de y = ax + b)
* `right_variance`: variance des données d’entrée sur le côté droit du fractionnement
* `right_rvariance`: variance résiduelle des données d’entrée sur le côté droit du fractionnement
* `left_rsquare`: r-Square de la ligne sur le côté gauche du fractionnement, consultez [series_fit_line ()](series-fit-linefunction.md)
* `left_slope`: pente de la ligne approximative gauche (il s’agit d’un de y = ax + b)
* `left_interception`: interception de la ligne gauche approximative (il s’agit de b de y = ax + b)
* `left_variance`: variance des données d’entrée sur le côté gauche du fractionnement
* `left_rvariance`: variance résiduelle des données d’entrée sur le côté gauche du fractionnement

*Notez* que cette fonction retourne plusieurs colonnes, par conséquent, elle ne peut pas être utilisée comme argument pour une autre fonction.

**Syntaxe**

projet `series_fit_2lines(` *x*`)`
* Renvoie toutes les colonnes mentionnées ci-dessus avec les noms suivants : series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx et etc.
projet (RS, si, v) = `series_fit_2lines(` *x*`)`
* Renverra les colonnes suivantes : RS (r-Square), si (index fractionné), v (variance) et le reste ressemblent à series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit et etc. extend (RS, si, v) = `series_fit_2lines(` *x*`)`
* Renvoie uniquement : rs (r-square), si (split index) et v (variance).
  
**Arguments**

* *x*: tableau dynamique de valeurs numériques.  

> [!TIP]
> La méthode la plus pratique pour utiliser cette fonction consiste à l’appliquer aux résultats de l’opérateur [Make-Series](make-seriesoperator.md) .

**Exemples**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="Série fit 2 lignes":::
