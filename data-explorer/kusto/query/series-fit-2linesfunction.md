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
ms.openlocfilehash: a364361ee5e5e260436486db24f1b61e2c21cbc9
ms.sourcegitcommit: 9fc3d8b396dddd2e1d9912845ba7bcc8e31c0267
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84720908"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

Applique deux segments de régression linéaire sur une série, en retournant plusieurs colonnes.  

Prend une expression contenant un tableau numérique dynamique comme entrée et applique [deux segments de régression linéaire](https://en.wikipedia.org/wiki/Segmented_regression) pour identifier et quantifier une modification de tendance dans une série. La fonction effectue une itération sur les index de série. Dans chaque itération, la fonction divise la série en deux parties, ajuste une ligne distincte (à l’aide de [series_fit_line ()](series-fit-linefunction.md)) à chaque partie et calcule le r-carré total. La meilleure séparation est celle qui optimise la valeur r-square ; la fonction renvoie ses paramètres :


|Paramètre  |Description  |
|---------|---------|
|`rsquare`     | [R-Square est une](https://en.wikipedia.org/wiki/Coefficient_of_determination) mesure standard de la qualité adaptée. Il s’agit d’un nombre dans la plage [0-1], où 1-est le meilleur ajustement possible, et 0 signifie que les données sont non ordonnées et ne correspondent à aucune ligne.        |
|`split_idx`     |   Index de point de rupture à deux segments (de base zéro).      |
|`variance`     | Variance des données d’entrée.        |
|`rvariance`     | Variance résiduelle, qui est l’écart entre les valeurs des données d’entrée et les valeurs approximatives (par segments de ligne).        |
|`line_fit`     | Tableau numérique contenant une série de valeurs de la ligne la mieux adaptée. La longueur de la série est égale à la longueur du tableau d’entrée. Il est principalement utilisé pour la représentation graphique.        |
|`right_rsquare`     | R : carré de la ligne sur le côté droit du fractionnement, consultez [series_fit_line ()](series-fit-linefunction.md).        |
|`right_slope`     | Pente de la ligne approximative droite (de la forme y = ax + b).         |
|`right_interception`     |  Interception de la ligne gauche approximative (b de y = ax + b).       |
|`right_variance`    | Variance des données d’entrée sur le côté droit du fractionnement.        |
|`right_rvariance`     | Variance résiduelle des données d’entrée sur le côté droit du fractionnement.        |
|`left_rsquare`     | R : carré de la ligne sur le côté gauche du fractionnement, consultez [series_fit_line ()](series-fit-linefunction.md).        |
|`left_slope`    | Pente de la ligne proche de gauche (de la forme y = ax + b).        |
|`left_interception`     |   Interception de la ligne gauche approximative (de la forme y = ax + b).      |
|`left_variance`     | Variance des données d’entrée sur le côté gauche du fractionnement.        |
|`left_rvariance`     | Variance résiduelle des données d’entrée sur le côté gauche du fractionnement.        |


> [!Note]
> Cette fonction retourne plusieurs colonnes et ne peut donc pas être utilisée en tant qu’argument pour une autre fonction.

**Syntaxe**

projet `series_fit_2lines(` *x*`)`
* Renvoie toutes les colonnes mentionnées ci-dessus avec les noms suivants : series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx etc.

projet (RS, si, v) = `series_fit_2lines(` *x*`)`
* Renverra les colonnes suivantes : RS (r-Square), si (index fractionné), v (variance) et le reste ressemblent à series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit et etc.

Extend (RS, si, v) = `series_fit_2lines(` *x*`)`
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
