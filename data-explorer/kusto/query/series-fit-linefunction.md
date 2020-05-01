---
title: series_fit_line ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit series_fit_line () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1694b92293b19cb84e40d38d667c8d67c1cc250f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618686"
---
# <a name="series_fit_line"></a>series_fit_line()

Applique la régression linéaire sur une série, en retournant plusieurs colonnes.  

Prend une expression contenant un tableau numérique dynamique comme entrée et effectue une [régression linéaire](https://en.wikipedia.org/wiki/Line_fitting) afin de trouver la ligne qui la correspond le mieux. Cette fonction doit être utilisée sur des tableaux de séries chronologiques, pour correspondre à la sortie de l’opérateur make-series. Il génère les colonnes suivantes :
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) est une mesure standard de la qualité adaptée. Il s’agit d’un nombre dans la plage [0-1], où 1 est la meilleure correspondance possible, et 0 signifie que les données sont totalement désordonnées et ne correspondent à aucune ligne 
* `slope`: pente de la ligne approximative (il s’agit d’un de y = ax + b)
* `variance`: variance des données d’entrée
* `rvariance`: variance résiduelle qui est l’écart entre les valeurs des données d’entrée et les valeurs approximatives.
* `interception`: interception de la ligne approximative (il s’agit de b de y = ax + b)
* `line_fit`: tableau numérique contenant une série de valeurs de la ligne la mieux adaptée. La longueur de la série est égale à la longueur du tableau d’entrée. Elle est principalement utilisée pour les graphiques.

**Syntaxe**

`series_fit_line(`*x*`)`

**Arguments**

* *x*: tableau dynamique de valeurs numériques.

> [!TIP]
> La méthode la plus pratique pour utiliser cette fonction consiste à l’appliquer aux résultats de l’opérateur [Make-Series](make-seriesoperator.md) .

**Exemples**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="Ligne ajustée des séries":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1,064, 3,7945, 6,526, 9,256, 11,987, 14,718, 17,449, 20,180, 22,910, 25,641, 28,371, 31,102 |