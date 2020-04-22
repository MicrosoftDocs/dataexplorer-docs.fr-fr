---
title: series_fit_2lines_dynamic() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_fit_2lines_dynamic() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: efb2743789c363a33e21ba472a945087b9b277cf
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663515"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

Applique deux segments de régression linéaire sur une série, retour objet dynamique.  

Prend une expression contenant un tableau numérique dynamique comme entrée et applique [deux segments de régression linéaire](https://en.wikipedia.org/wiki/Segmented_regression) afin d’identifier et de quantifier le changement de tendance dans une série. La fonction s’adapte sur les index de la série et dans chaque itération divise la série à 2 parties, s’adapte à une ligne séparée (en utilisant [series_fit_line)ou](series-fit-linefunction.md) [series_fit_line_dynamic))](series-fit-line-dynamicfunction.md)) à chaque partie et calcule le r-carré total. La meilleure scission est celle qui a maximisé r-carré; la fonction retourne ses paramètres en valeur dynamique avec le contenu suivant :
* `rsquare`: [r-square](https://en.wikipedia.org/wiki/Coefficient_of_determination) est une mesure standard de la qualité de l’ajustement. Il s’agit d’un nombre dans la plage [0-1], où 1 est la meilleure correspondance possible, et 0 signifie que les données sont totalement désordonnées et ne correspondent à aucune ligne
* `split_idx`: l’indice du point de rupture à 2 segments (zéro à base)
* `variance`: variance des données d’entrée
* `rvariance`: écart résiduel qui est la variance entre les données d’entrée valorise les approximées (par les 2 segments de ligne).
* `line_fit`: tableau numérique tenant une série de valeurs de la ligne la mieux ajustée. La longueur de la série est égale à la longueur du tableau d’entrée. Elle est principalement utilisée pour les graphiques.
* `right.rsquare`: r-carré de la ligne sur le côté droit de la scission, voir [series_fit_line()](series-fit-linefunction.md) ou[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)
* `right.slope`: pente de la ligne approximative droite (c’est un de y’ax’b)
* `right.interception`: interception de la ligne gauche approximative (c’est b de y’ax-b)
* `right.variance`: variance des données d’entrée sur le côté droit de la scission
* `right.rvariance`: écart résiduel des données d’entrée sur le côté droit de la scission
* `left.rsquare`: r-carré de la ligne sur le côté gauche de la scission, voir [series_fit_line()](series-fit-linefunction.md) ou [series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)
* `left.slope`: pente de la ligne approximative gauche (c’est un de y’ax’b)
* `left.interception`: interception de la ligne gauche approximative (c’est b de y’ax-b)
* `left.variance`: variance des données d’entrée sur le côté gauche de la scission
* `left.rvariance`: écart résiduel des données d’entrée sur le côté gauche de la scission

Cet opérateur est similaire à [series_fit_2lines,](series-fit-2linesfunction.md)mais contrairement à `series-fit-2lines` ce qu’il retourne un sac dynamique.

**Syntaxe**

`series_fit_2lines_dynamic(`*X*`)`

**Arguments**

* *x*: Gamme dynamique de valeurs numériques.  

> [!TIP]
> La façon la plus pratique d’utiliser cette fonction est de l’appliquer aux résultats de [l’opérateur de la série make.](make-seriesoperator.md)

**Exemples**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/samples/series-fit-2lines.png" alt-text="Série ajustement 2 lignes":::
