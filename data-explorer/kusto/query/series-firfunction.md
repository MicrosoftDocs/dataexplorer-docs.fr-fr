---
title: series_fir() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_fir() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: bbdf93504be431d77c19a881cf79d1e1d3d400ac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663574"
---
# <a name="series_fir"></a>series_fir()

Applique un filtre De réponse Finie Impulse sur une série.  

Prend une expression contenant un tableau numérique dynamique comme entrée et applique un filtre [De réponse Finie Impulse.](https://en.wikipedia.org/wiki/Finite_impulse_response) En spécifiant les `filter` coefficients, il peut être utilisé pour calculer la moyenne mobile, lisser, modifier la détection et bien d’autres cas d’utilisation. La fonction accepte comme entrée la colonne qui contient le tableau dynamique et un tableau statique et dynamique des coefficients du filtre, et applique le filtre sur la colonne. Elle génère une nouvelle colonne de tableau dynamique, qui contient la sortie filtrée.  

**Syntaxe**

`series_fir(`*x* `,` *filtre* `,` *[normaliser*`,` *[centre*]]`)`

**Arguments**

* *x*: Cellule de tableau dynamique qui est un éventail de valeurs numériques, généralement la sortie résultante des opérateurs [de make-series](make-seriesoperator.md) ou [de make_list.](makelist-aggfunction.md)
* *filtre*: Une expression constante contenant les coefficients du filtre (stocké comme un tableau dynamique de valeurs numériques).
* *normaliser*: Valeur Boolean facultative indiquant si le filtre doit être normalisé (c’est-à-dire divisé par la somme des coefficients). Si *le filtre* contient des valeurs négatives, puis `null` *normaliser* doit être spécifié comme `false`, autrement résultat sera . S’il n’est pas spécifié, une valeur par défaut de *normalisation* sera supposée en fonction de la `false`présence de valeurs négatives dans le *filtre*: si le *filtre* contient au moins une valeur négative, on suppose que la *normalisation* est .  
La normalisation est un moyen pratique de s’assurer que la somme des coefficients est de 1 et donc le filtre n’amplifie pas ou n’atténue pas la série. Par exemple, la moyenne mobile de 4 bacs pourrait être spécifiée par *filtre*[1,1,1,1] et *normalisée*,vrai, ce qui est plus facile que de taper [0.25,0.25.0.25,0.25].
* *centre*: Une valeur Boolean optionnelle indiquant si le filtre est appliqué symétriquement sur une fenêtre de temps avant et après le point actuel, ou sur une fenêtre temporelle du point de vue actuel vers l’arrière. Par défaut, center est défini sur false, ce qui correspond au scénario de données de streaming, où nous pouvons uniquement appliquer le filtre sur les points actuels et anciens. Toutefois, pour un traitement ad hoc, vous pouvez le définir sur true, et maintenir la synchronisation avec la série chronologique (voir les exemples ci-dessous). Techniquement parlant, ce paramètre contrôle le retard de [groupe](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)du filtre.

**Exemples**

* Le calcul d’une moyenne mobile de 5 points peut être effectué en définissant le *filtre*[1,1,1,1,1] et *la* = `true` normalisation (par défaut). Notez l’effet du *centre* = `false` (par défaut) par rapport à `true`:

```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

Cette requête renvoie :  
*5h_MovingAvg*: filtre moyen mobile de 5 points. Le pic est lissé et son point culminant est déplacé de (5-1)/2 = 2 h.  
*5h_MovingAvg_centered*: même mais avec le centre de réglage-vrai, provoque le pic de rester dans son emplacement d’origine.

:::image type="content" source="images/samples/series-fir.png" alt-text="Sapin de série":::

* Le calcul de la différence entre un point et son précédent peut être effectué en définissant le *filtre*[1,-1] :

```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```
:::image type="content" source="images/samples/series-fir2.png" alt-text="Sapin de série 2":::
