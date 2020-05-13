---
title: series_fit_2lines_dynamic ()-Azure Explorateur de données
description: Cet article décrit series_fit_2lines_dynamic () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6f1a6e4a80bfbc02f9e6f552ceca2ba1bb54eb08
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372726"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

Applique deux segments de régression linéaire sur une série, en retournant un objet dynamique.  

Prend une expression contenant un tableau numérique dynamique comme entrée et applique [deux segments de régression linéaire](https://en.wikipedia.org/wiki/Segmented_regression) pour identifier et quantifier les modifications de tendance dans une série. La fonction effectue une itération sur les index de série. Dans chaque itération, il fractionne la série en deux parties et ajuste une ligne distincte à l’aide de [series_fit_line ()](series-fit-linefunction.md) ou [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md). La fonction s’ajuste aux lignes de chacune des deux parties, puis calcule la valeur totale du R au carré. La meilleure Division est celle qui maximise le carré R. La fonction retourne ses paramètres en valeur dynamique avec le contenu suivant :

* `rsquare`: [R-squared](https://en.wikipedia.org/wiki/Coefficient_of_determination) est une mesure standard de la qualité adaptée. Il s’agit d’un nombre compris dans la plage [0-1], où 1 est la meilleure correspondance possible, et 0 signifie que les données ne sont pas ordonnées et ne correspondent à aucune ligne.
* `split_idx`: index de point de rupture à deux segments (de base zéro).
* `variance`: variance des données d’entrée.
* `rvariance`: variance résiduelle qui est l’écart entre les valeurs des données d’entrée et les valeurs approximatives (par les deux segments de ligne).
* `line_fit`: tableau numérique contenant une série de valeurs de la ligne la mieux adaptée. La longueur de la série est égale à la longueur du tableau d’entrée. Elle est utilisée pour les graphiques.
* `right.rsquare`: r-Square de la ligne sur le côté droit du fractionnement, consultez [series_fit_line ()](series-fit-linefunction.md) ou [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md).
* `right.slope`: pente de la ligne approximative droite (de la forme y = ax + b).
* `right.interception`: interception de la ligne gauche approximative (b de y = ax + b).
* `right.variance`: variance des données d’entrée sur le côté droit du fractionnement.
* `right.rvariance`: variance résiduelle des données d’entrée sur le côté droit du fractionnement.
* `left.rsquare`: r-Square de la ligne sur le côté gauche du fractionnement, consultez [series_fit_line ()]. (series-fit-linefunction.md) ou [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md).
* `left.slope`: pente de la ligne proche de gauche (sous la forme y = ax + b).
* `left.interception`: interception de la ligne gauche approximative (de la forme y = ax + b).
* `left.variance`: variance des données d’entrée sur le côté gauche du fractionnement.
* `left.rvariance`: variance résiduelle des données d’entrée sur le côté gauche du fractionnement.

Cet opérateur est semblable à [series_fit_2lines](series-fit-2linesfunction.md). Contrairement à `series-fit-2lines` , elle retourne un sac dynamique.

**Syntaxe**

`series_fit_2lines_dynamic(`*x*`)`

**Arguments**

* *x*: tableau dynamique de valeurs numériques.  

> [!TIP]
> La méthode la plus pratique pour utiliser cette fonction consiste à l’appliquer aux résultats de l’opérateur [Make-Series](make-seriesoperator.md) .

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="Série fit 2 lignes":::
