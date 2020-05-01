---
title: series_decompose_anomalies ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit series_decompose_anomalies () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 51ac499690323b1d2bafb4dc20ab7773f5c99c63
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618888"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

Détection des anomalies basée sur la décomposition des séries (reportez-vous à [series_decompose ()](series-decomposefunction.md)) 

Prend une expression contenant une série (tableau numérique dynamique) comme entrée et extrait des points anormaux avec des scores.

**Syntaxe**

`series_decompose_anomalies (`*Tendance* à la tendance`, ` des *seuils* `,` *Seasonality* `,` de *série* `[, ` *Test_points* `, ` *AD_method* `,` *Seasonality_threshold*`])`

**Arguments**

* *Série*: cellule de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs [Make-Series](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md)
* *Seuil*: seuil d’anomalies, 1,5 par défaut (valeur k) pour la détection des anomalies légères ou plus fortes
* Caractère *saisonnier*: un entier contrôlant l’analyse saisonnière, contenant soit
    * -1 : détection automatique du caractère saisonnier (à l’aide de [series_periods_detect](series-periods-detectfunction.md)) [Default] 
    * 0 : aucun caractère saisonnier (c’est-à-dire ignorer l’extraction de ce composant)
    * period : entier positif, qui spécifie la période attendue en nombre d’unités d’emplacements. Par exemple, si la série est dans des emplacements 1H, une période hebdomadaire est de 168 emplacements
* *Tendance*: chaîne contrôlant l’analyse des tendances, contenant soit    
    * « AVG » : définir le composant de tendance en moyenne des séries [par défaut]
    * « None » : aucune tendance, ignorer l’extraction de ce composant 
    * « linefit » : extraction du composant de tendance à l’aide de la régression linéaire
* *Test_points*: 0 [Default] ou un entier positif spécifiant le nombre de points à la fin de la série à exclure du processus d’apprentissage (régression). Ce paramètre doit être défini à des fins de prévision
* *AD_method*: chaîne contrôlant la méthode de détection des anomalies (consultez [series_outliers](series-outliersfunction.md)) sur les séries chronologiques résiduelles, contenant soit    
    * « ctukey » : [test de la limite de Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) avec une plage de centile personnalisée du dixième au dixième [par défaut]
    * « Tukey » : [test de la limite de Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) avec une plage de centile standard de 25-75e
* *Seasonality_threshold*: le seuil du score saisonnier lorsque le caractère *saisonnier* est défini sur détection automatique, le seuil du `0.6` score par défaut est (pour plus d’informations, consultez : [series_periods_detect](series-periods-detectfunction.md))


**Renvoi**

 La fonction retourne les séries correspondantes suivantes :

* `ad_flag`: série ternaire contenant (+ 1,-1, 0) marquage/baisse/aucune anomalie, respectivement
* `ad_score`: score d’anomalie
* `baseline`: valeur prédite de la série en fonction de la décomposition

**En savoir plus sur l’algorithme**

Cette fonction suit les étapes suivantes :
1. Appelle [series_decompose ()](series-decomposefunction.md) avec les paramètres respectifs pour créer la série de lignes de base et les résidus.
2. Calcule ad_score série en appliquant [series_outliers ()](series-outliersfunction.md) avec la méthode de détection d’anomalies choisie sur la série des résidus
3. Calcule la série de ad_flag en appliquant le seuil sur la ad_score à marquer/baisser/pas d’anomalie, respectivement
 
**Exemples**

**1. détection des anomalies dans le caractère saisonnier hebdomadaire**

Dans l’exemple suivant, nous générons une série avec un caractère saisonnier hebdomadaire, puis nous y ajoutons quelques valeurs hors norme. `series_decompose_anomalies`détecte automatiquement le caractère saisonnier et génère une ligne de base qui capture le modèle répétitif. Les valeurs hors norme que nous avons ajoutées peuvent être clairement repérées dans le composant ad_score.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers.png" alt-text="Caractère saisonnier hebdomadaire présentant les lignes de base et les valeurs hors norme" border="false":::

**2. détection des anomalies dans le caractère saisonnier hebdomadaire avec tendance**

Dans cet exemple, nous ajoutons une tendance à la série de l’exemple précédent. Tout d’abord, `series_decompose_anomalies` nous exécutons avec les paramètres par défaut `avg` dans lesquels la valeur par défaut de tendance prend uniquement en compte la moyenne et ne calcule pas la tendance. nous pouvons voir que la ligne de base générée ne contient pas la tendance et qu’elle est moins précise que l’exemple précédent. par conséquent, certaines des valeurs hors norme que nous avons insérées dans les données ne sont pas détectées

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart   
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers-with-trend.png" alt-text="Résultats saisonniers hebdomadaires avec tendance" border="false":::

Ensuite, nous exécutons le même exemple, mais étant donné que nous attendions une tendance dans la `linefit` série, nous spécifions dans le paramètre Trend. Nous pouvons voir que la ligne de base est plus proche de la série d’entrée. Tous les valeurs hors norme que nous avons insérées sont détectées, ainsi que des faux positifs (Voir l’exemple suivant sur le réglage du seuil).

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 1.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-linefit-trend.png" alt-text="Anomalies saisonnieres hebdomadaires avec tendance linefit" border="false":::

**3. modification du seuil de détection des anomalies**

Dans l’exemple précédent, quelques points bruyants ont été détectés comme des anomalies. dans cet exemple, nous augmentons le seuil de détection des anomalies d’une valeur par défaut de 1,5 à 2,5 la plage intercentile afin que seules les anomalies plus fortes soient détectées. Nous pouvons voir que seules les valeurs hors norme que nous avons insérées dans les données sont détectées.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and onlgoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 2.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-higher-threshold.png" alt-text="Série hebdomadaire d’anomalies avec un seuil d’anomalies plus élevé" border="false":::

