---
title: series_fir ()-Azure Explorateur de données
description: Cet article décrit series_fir () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 616fee7b0a1b6852f66d3db22846b2645e03135f
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84665008"
---
# <a name="series_fir"></a>series_fir()

Applique un filtre de réponse impulsionnelle finie (FIR) sur une série.  

La fonction prend une expression contenant un tableau numérique dynamique comme entrée et applique un filtre à [réponse impulsionnelle finie](https://en.wikipedia.org/wiki/Finite_impulse_response) . En spécifiant les `filter` coefficients, vous pouvez les utiliser pour calculer la moyenne mobile, le lissage, la détection des modifications et bien d’autres cas d’utilisation. La fonction prend la colonne qui contient le tableau dynamique et un tableau dynamique statique des coefficients du filtre comme entrée, et applique le filtre sur la colonne. Elle génère une nouvelle colonne de tableau dynamique, qui contient la sortie filtrée.  

**Syntaxe**

`series_fir(`*x* `,` *filtre* x [ `,` *normaliser*[ `,` *Centre*]]`)`

**Arguments**

* *x*: cellule de tableau dynamique de valeurs numériques. En général, le résultat des opérateurs [Make-Series](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md) .
* *filtre*: expression constante contenant les coefficients du filtre (stocké sous la forme d’un tableau dynamique de valeurs numériques).
* *Normalize*: valeur booléenne facultative indiquant si le filtre doit être normalisé. Autrement dit, divisé par la somme des coefficients. Si le filtre contient des valeurs négatives, *Normalize* doit être spécifié comme `false` . sinon, le résultat sera `null` . S’il n’est pas spécifié, une valeur par défaut de *Normalize* est supposée, en fonction de la présence de valeurs négatives dans le *filtre*. Si *Filter* contient au moins une valeur négative, *Normalize* est supposé être `false` .  
La normalisation est un moyen pratique de s’assurer que la somme des coefficients est 1. Le filtre n’amplifie pas ou n’atténue pas la série. Par exemple, la moyenne mobile de quatre emplacements peut être spécifiée par *Filter*= [1, 1, 1, 1] et *normalized*= true, ce qui est plus facile que de taper [0.25, 0.25.0.25, 0.25].
* *Center*: valeur booléenne facultative qui indique si le filtre est appliqué symétriquement sur une fenêtre de temps avant et après le point actuel, ou sur une fenêtre de temps à partir du point actuel vers l’arrière. Par défaut, Center a la valeur false, ce qui correspond au scénario de données de streaming, où nous pouvons uniquement appliquer le filtre sur les points actuels et antérieurs. Toutefois, pour un traitement ad hoc, vous pouvez le définir sur `true` , en le conservant à la synchronisation avec la série chronologique. Voir les exemples ci-dessous. Ce paramètre contrôle le délai de [groupe](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)du filtre.

**Exemples**

* Calculez une moyenne mobile de cinq points en définissant *Filter*= [1, 1, 1, 1, 1] et *Normalize* = `true` (par défaut). Notez l’effet de *Center* = `false` (par défaut) par rapport à `true` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

Cette requête renvoie :  
*5h_MovingAvg*: filtre de moyenne mobile de cinq points. Le pic est lissé et son point culminant est déplacé de (5-1)/2 = 2 h.  
*5h_MovingAvg_centered*: même, mais en définissant `center=true` , le pic reste à son emplacement d’origine.

:::image type="content" source="images/series-firfunction/series-fir.png" alt-text="Sapin de série" border="false":::

* Pour calculer la différence entre un point et son précédent, définissez *Filter*= [1,-1].

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```

:::image type="content" source="images/series-firfunction/series-fir2.png" alt-text="Série FIR 2" border="false":::
