---
title: series_decompose_forecast ()-Azure Explorateur de données
description: Cet article décrit series_decompose_forecast () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 9676da9d12e2654cd4d92538f183a2630971d078
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372879"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

Prévisions basées sur la décomposition des séries.

Prend une expression contenant une série (tableau numérique dynamique) comme entrée et prédit les valeurs des derniers points de fin (reportez-vous à [series_decompose](series-decomposefunction.md) pour plus d’informations sur la méthode de décomposition).
 
**Syntaxe**

`series_decompose_forecast(`*Série* `,` *Points* `[,` Caractère *saisonnier* `,` *Tendance* `,` *Seasonality_threshold*`])`

**Arguments**

* *Série*: cellule de tableau dynamique qui est un tableau de valeurs numériques. En général, le résultat des opérateurs [Make-Series](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md) .
* *Points*: entier spécifiant le nombre de points à la fin de la série à prédire (prévision). Ces points sont exclus du processus d’apprentissage (régression).
* Caractère *saisonnier*: un entier contrôlant l’analyse saisonnière, contenant l’un des éléments suivants :
    * -1 : détection automatique du caractère saisonnier à l’aide de [series_periods_detect](series-periods-detectfunction.md) (valeur par défaut). 
    * period : entier positif, qui spécifie la période attendue en nombre d’emplacements. Par exemple, si la série est dans des emplacements 1H, une période hebdomadaire est de 168 emplacements.
    * 0 : pas de caractère saisonnier (ignorer l’extraction de ce composant).   
* *Tendance*: chaîne contrôlant l’analyse des tendances, contenant l’un des éléments suivants :
    * « linefit » : extraction du composant de tendance à l’aide de la régression linéaire (par défaut).    
    * « AVG » : définir le composant de tendance comme moyenne (x).
    * « None » : aucune tendance. ignorez l’extraction de ce composant.   
* *Seasonality_threshold*: le seuil du score saisonnier lorsque le caractère *saisonnier* est défini sur détection automatique, le seuil de score par défaut est `0.6` . Pour plus d’informations, consultez [series_periods_detect](series-periods-detectfunction.md).

**Renvoi**

 Tableau dynamique avec la série prévue
  

**Remarques**

* Le tableau dynamique de la série d’entrée d’origine doit inclure un emplacement de *points* de nombre à prévoir. pour ce faire, utilisez la [série make](make-seriesoperator.md) et spécifiez l’heure de fin dans la plage qui inclut la période à prévoir.
    
* Les fonctions saisonnier et/ou tendance doivent être activées ; sinon, la fonction est redondante et retourne simplement une série remplie de zéros.

**Exemple**

Dans l’exemple suivant, nous générons une série de 4 semaines dans un grain horaire avec un caractère saisonnier hebdomadaire et une petite tendance vers le haut, nous utilisons `make-series` et ajoutons une autre semaine vide à la série. `series_decompose_forecast`est appelé avec une semaine (24 * 7 points), il détecte automatiquement le caractère saisonnier et la tendance et génère une prévision de l’intégralité de la période de 5 semaines. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/series-decompose-forecastfunction/series-decompose-forecast.png" alt-text="Plan de décomposition de série":::
