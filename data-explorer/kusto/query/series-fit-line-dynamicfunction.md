---
title: series_fit_line_dynamic() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_fit_line_dynamic() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 03bbb143504d0540156d5f18d4ed527ab5d13d38
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663559"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

Applique la régression linéaire sur une série, retour d’objet dynamique.  

Prend une expression contenant un tableau numérique dynamique comme entrée et effectue [une régression linéaire](https://en.wikipedia.org/wiki/Line_fitting) afin de trouver la ligne qui lui convient le mieux. Cette fonction doit être utilisée sur des tableaux de séries chronologiques, pour correspondre à la sortie de l’opérateur make-series. Il génère une valeur dynamique avec le contenu suivant :
* `rsquare`: [r-square](https://en.wikipedia.org/wiki/Coefficient_of_determination) est une mesure standard de la qualité de l’ajustement. Il s’agit d’un nombre dans la plage [0-1], où 1 est la meilleure correspondance possible, et 0 signifie que les données sont totalement désordonnées et ne correspondent à aucune ligne 
* `slope`: pente de la ligne approximative (c’est un de y’ax’b)
* `variance`: variance des données d’entrée
* `rvariance`: écart résiduel qui est la variance entre les données d’entrée valorise les données approximatives.
* `interception`: interception de la ligne approximative (c’est b de y’ax-b)
* `line_fit`: tableau numérique tenant une série de valeurs de la ligne la mieux ajustée. La longueur de la série est égale à la longueur du tableau d’entrée. Elle est principalement utilisée pour les graphiques.

Cet opérateur est similaire à [series_fit_line,](series-fit-linefunction.md)mais contrairement à `series-fit-line` ce qu’il retourne un sac dynamique.

**Syntaxe**

`series_fit_line_dynamic(`*X*`)`

**Arguments**

* *x*: Gamme dynamique de valeurs numériques.

> [!TIP]
> La façon la plus pratique d’utiliser cette fonction est de l’appliquer aux résultats de [l’opérateur de la série make.](make-seriesoperator.md)

**Exemples**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend fit=series_fit_line_dynamic(y)
| extend RSquare=fit.rsquare, Slope=fit.slope, Variance=fit.variance,RVariance=fit.rvariance,Interception=fit.interception,LineFit=fit.line_fit
| render timechart
```

:::image type="content" source="images/samples/series-fit-line.png" alt-text="Ligne d’ajustement de série":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064, 3.7945, 6.526, 9.256, 11.987, 14.718, 17.449, 20.180, 22.910, 25.641, 28.371, 31.102 |