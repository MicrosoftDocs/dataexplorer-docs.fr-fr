---
title: series_decompose_forecast() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_decompose_forecast() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 67f464949bbc432e73932c4d8bff8290ef8f0f8f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663542"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

Prévisions basées sur la décomposition des séries.

Prend une expression contenant une série (tableau numérique dynamique) comme entrée et de prédire les valeurs des derniers points de fuite (se référer à [series_decompose](series-decomposefunction.md) pour plus de détails sur la méthode de décomposition).
 
**Syntaxe**

`series_decompose_forecast(`*Série* `,` *Points* `[,` *Seasonality* `,` *Trend* `,` *Seasonality_threshold*`])`

**Arguments**

* *Série*: Cellule de tableau dynamique qui est un éventail de valeurs numériques. Typiquement la sortie résultante des opérateurs [de série](make-seriesoperator.md) ou [de make_list.](makelist-aggfunction.md)
* *Points*: Integer précisant le nombre de points à la fin de la série à prévoir (prévisions). Ces points sont exclus du processus d’apprentissage (régression).
* *Saisonnalité*: Un integer contrôlant l’analyse saisonnière, contenant l’un des éléments suivants :
    * -1 : auto-détection de la saisonnalité à [l’aide de series_periods_detect](series-periods-detectfunction.md) (par défaut). 
    * période : integer positif, précisant la période prévue en nombre de bacs. Par exemple, si la série est dans 1h poubelles, une période hebdomadaire est de 168 bacs.
    * 0: pas de saisonnalité (sauter l’extraction de ce composant).   
* *Tendance*: Une chaîne contrôlant l’analyse de tendance, contenant l’une des suivantes :
    * "linefit": extraire le composant tendance à l’aide de la régression linéaire (par défaut).    
    * "avg": définir la composante tendance comme moyenne(x).
    * "aucun": pas de tendance, sauter l’extraction de ce composant.   
* *Seasonality_threshold*: Le seuil de la saisonnalité lorsque *la saisonnalité* est réglée `0.6`pour détecter automatiquement, le seuil de score par défaut est . Pour plus d’informations, voir [series_periods_detect](series-periods-detectfunction.md).

**Retour**

 Un tableau dynamique avec la série prévisionnée
  

**Remarques**

* La gamme dynamique de la série d’entrées originale devrait inclure un *nombre* de points à prévoir, cela se fait généralement en utilisant la série [de make-et](make-seriesoperator.md) en spécifiant l’heure de fin dans la plage qui comprend le délai de prévision.
    
* Soit la saisonnalité et / ou la tendance doit être activée, sinon la fonction est redondante et retourne juste une série remplie de zéros.

**Exemple**

Dans l’exemple suivant, nous produisons une série de 4 semaines dans un grain `make-series` horaire avec une saisonnalité hebdomadaire et une petite tendance à la hausse, nous utilisons ensuite et ajoutons une autre semaine vide à la série. `series_decompose_forecast`est appelé avec une semaine (24-7 points), il détecte automatiquement la saisonnalité et la tendance et génère une prévision de l’ensemble de la période de 5 semaines. 

```kusto
let ts=range t from 1 to 24*7*4 step 1 // generate 4 weeks of hourly data
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| make-series y=max(y) on Timestamp in range(datetime(2018-03-01 05:00), datetime(2018-03-01 05:00)+24*7*5h, 1h); // create a time series of 5 weeks (last week is empty)
ts 
| extend y_forcasted = series_decompose_forecast(y, 24*7)  // forecast a week forward
| render timechart 
```

:::image type="content" source="images/samples/series-decompose-forecast.png" alt-text="Prévisions de décomposition de la série":::
