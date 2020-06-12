---
title: series_fit_line ()-Azure Explorateur de données
description: Cet article décrit series_fit_line () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f0401a5b10d2feb74c629e6b04b127e6d36057ad
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717170"
---
# <a name="series_fit_line"></a>series_fit_line()

Applique la régression linéaire sur une série, en retournant plusieurs colonnes.  

Accepte une expression contenant un tableau numérique dynamique comme entrée et effectue une [régression linéaire](https://en.wikipedia.org/wiki/Line_fitting) pour trouver la ligne qui la correspond le mieux. Cette fonction doit être utilisée sur des tableaux de séries chronologiques, pour correspondre à la sortie de l’opérateur make-series. La fonction génère les colonnes suivantes :
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) est une mesure standard de la qualité adaptée. La valeur est un nombre compris dans la plage [0-1], où 1-est le meilleur ajustement possible, et 0 signifie que les données ne sont pas ordonnées et ne correspondent à aucune ligne. 
* `slope`: Pente de la ligne approximative ("a" à partir de y = ax + b).
* `variance`: Variance des données d’entrée.
* `rvariance`: Variance résiduelle qui est l’écart entre les valeurs des données d’entrée et les valeurs approximatives.
* `interception`: Interception de la ligne approximative ("b" à partir de y = ax + b).
* `line_fit`: Tableau numérique contenant une série de valeurs de la ligne la mieux adaptée. La longueur de la série est égale à la longueur du tableau d’entrée. Valeur utilisée pour la représentation graphique.

**Syntaxe**

`series_fit_line(`*x*`)`

**Arguments**

* *x*: tableau dynamique de valeurs numériques.

> [!TIP]
> La méthode la plus pratique pour utiliser cette fonction consiste à l’appliquer aux résultats de l’opérateur [Make-Series](make-seriesoperator.md) .

**Exemples**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="Ligne ajustée des séries":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1,064, 3,7945, 6,526, 9,256, 11,987, 14,718, 17,449, 20,180, 22,910, 25,641, 28,371, 31,102 |
