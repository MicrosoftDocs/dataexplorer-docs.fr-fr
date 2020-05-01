---
title: series_fir ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit series_fir () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 4641da6481365d13919de59708387b689581c9ce
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618793"
---
# <a name="series_fir"></a>series_fir()

Applique un filtre à réponse impulsionnelle finie sur une série.  

Accepte une expression contenant un tableau numérique dynamique comme entrée et applique un filtre à [réponse impulsionnelle finie](https://en.wikipedia.org/wiki/Finite_impulse_response) . En spécifiant `filter` les coefficients, il peut être utilisé pour le calcul de la moyenne mobile, du lissage, de la détection des modifications et bien d’autres cas d’utilisation. La fonction accepte comme entrée la colonne qui contient le tableau dynamique et un tableau statique et dynamique des coefficients du filtre, et applique le filtre sur la colonne. Elle génère une nouvelle colonne de tableau dynamique, qui contient la sortie filtrée.  

**Syntaxe**

`series_fir(`*x*`,` *filtre* x [`,` *normaliser*[`,` *Centre*]]`)`

**Arguments**

* *x*: cellule de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs [Make-Series](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md) .
* *filtre*: expression constante contenant les coefficients du filtre (stocké sous la forme d’un tableau dynamique de valeurs numériques).
* *Normalize*: valeur booléenne facultative indiquant si le filtre doit être normalisé (c’est-à-dire divisé par la somme des coefficients). Si le *filtre* contient des valeurs négatives, *normaliser* doit être `false`spécifié comme `null`. sinon, le résultat sera. S’il n’est pas spécifié, une valeur par défaut de *Normalize* est prise en charge en fonction de la présence de valeurs négatives dans le *filtre*: si le *filtre* contient au moins une valeur négative `false`, la *normalisation* est supposée être.  
La normalisation est un moyen pratique de s’assurer que la somme des coefficients est 1 et que le filtre n’amplifie pas ou n’atténue pas la série. Par exemple, la moyenne mobile de 4 emplacements peut être spécifiée par *Filter*= [1, 1, 1, 1] et *normalized*= true, ce qui est plus facile que de taper [0.25, 0.25.0.25, 0.25].
* *Center*: valeur booléenne facultative indiquant si le filtre est appliqué symétriquement sur une fenêtre de temps avant et après le point actuel, ou sur une fenêtre de temps à partir du point actuel vers l’arrière. Par défaut, center est défini sur false, ce qui correspond au scénario de données de streaming, où nous pouvons uniquement appliquer le filtre sur les points actuels et anciens. Toutefois, pour un traitement ad hoc, vous pouvez le définir sur true, et maintenir la synchronisation avec la série chronologique (voir les exemples ci-dessous). Techniquement parlant, ce paramètre contrôle le délai de [groupe](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)du filtre.

**Exemples**

* Le calcul d’une moyenne mobile de 5 points peut être effectué en définissant *Filter*= [1, 1, 1, 1, 1] et *Normalize* = `true` (par défaut). Notez l’effet de *Center* = `false` (par défaut) par `true`rapport à :

```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

Cette requête renvoie :  
*5h_MovingAvg*: filtre de moyenne mobile de 5 points. Le pic est lissé et son point culminant est déplacé de (5-1)/2 = 2 h.  
*5h_MovingAvg_centered*: même mais avec le paramètre Center = true, le pic reste à son emplacement d’origine.

:::image type="content" source="images/series-firfunction/series-fir.png" alt-text="Sapin de série" border="false":::

* Le calcul de la différence entre un point et son précédent peut être effectué en définissant *Filter*= [1,-1] :

```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```

:::image type="content" source="images/series-firfunction/series-fir2.png" alt-text="Série FIR 2" border="false":::
