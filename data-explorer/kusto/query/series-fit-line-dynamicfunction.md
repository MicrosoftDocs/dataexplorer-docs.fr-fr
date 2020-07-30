---
title: series_fit_line_dynamic ()-Azure Explorateur de données
description: Cet article décrit series_fit_line_dynamic () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 80b54dce81799304a4297ee1192f2ee1475d2ec2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351520"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

Applique la régression linéaire sur une série, en retournant un objet dynamique.  

Prend une expression contenant un tableau numérique dynamique comme entrée, et effectue une [régression linéaire](https://en.wikipedia.org/wiki/Line_fitting) pour trouver la ligne qui la correspond le mieux. Cette fonction doit être utilisée sur des tableaux de séries chronologiques, pour correspondre à la sortie de l’opérateur make-series. Il génère une valeur dynamique avec le contenu suivant :
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) est une mesure standard de la qualité adaptée. Il s’agit d’un nombre dans la plage [0-1], où 1 est la meilleure correspondance possible, et 0 signifie que les données ne sont pas ordonnées et ne correspondent à aucune ligne.
* `slope`: Pente de la ligne approximative (valeur *a*-de *y = ax + b*)
* `variance`: Variance des données d’entrée
* `rvariance`: Variance résiduelle qui est l’écart entre les valeurs des données d’entrée et les valeurs approximatives.
* `interception`: Interception de la ligne approximative (valeur *b*de *y = ax + b*)
* `line_fit`: Tableau numérique contenant une série de valeurs de la ligne la mieux adaptée. La longueur de la série est égale à la longueur du tableau d’entrée. Elle est principalement utilisée pour les graphiques.

Cet opérateur est semblable à [series_fit_line](series-fit-linefunction.md), mais contrairement à, `series-fit-line` il retourne un conteneur dynamique.

## <a name="syntax"></a>Syntaxe

`series_fit_line_dynamic(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: tableau dynamique de valeurs numériques.

> [!TIP]
> La méthode la plus pratique pour utiliser cette fonction consiste à l’appliquer aux résultats de l’opérateur [Make-Series](make-seriesoperator.md) .

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend fit=series_fit_line_dynamic(y)
| extend RSquare=fit.rsquare, Slope=fit.slope, Variance=fit.variance,RVariance=fit.rvariance,Interception=fit.interception,LineFit=fit.line_fit
| render timechart
```
 
:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="Ligne ajustée des séries":::

| RSquare | Pente | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1,064, 3,7945, 6,526, 9,256, 11,987, 14,718, 17,449, 20,180, 22,910, 25,641, 28,371, 31,102 |
 
