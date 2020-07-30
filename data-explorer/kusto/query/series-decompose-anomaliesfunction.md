---
title: series_decompose_anomalies ()-Azure Explorateur de données
description: Cet article décrit series_decompose_anomalies () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 2191a26a0ee0bccd708c492690e58767d3cf52e9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345621"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

La détection des anomalies est basée sur la décomposition des séries.
Pour plus d’informations, consultez [series_decompose ()](series-decomposefunction.md).

La fonction prend une expression contenant une série (tableau numérique dynamique) comme entrée et extrait des points anormaux avec des scores.

## <a name="syntax"></a>Syntaxe

`series_decompose_anomalies (`*Série* `[, ` *Seuil* `,` Caractère *saisonnier* `,` *Tendance* `, ` *Test_points* `, ` *AD_method* `,` *Seasonality_threshold*`])`

## <a name="arguments"></a>Arguments

* *Série*: cellule de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs [Make-Series](make-seriesoperator.md) ou [make_list](makelist-aggfunction.md)
* *Seuil*: seuil d’anomalies, 1,5 par défaut (valeur k) pour la détection des anomalies légères ou plus fortes
* Caractère *saisonnier*: un entier contrôlant l’analyse saisonnière, contenant soit
    * -1 : détection automatique du caractère saisonnier (à l’aide de [series_periods_detect](series-periods-detectfunction.md)) [Default]
    * 0 : aucun caractère saisonnier (c’est-à-dire, ignorer l’extraction de ce composant)
    * period : entier positif, qui spécifie la période attendue en nombre d’unités d’emplacements. Par exemple, si la série est dans des emplacements d’une heure, une période hebdomadaire est de 168 emplacements
* *Tendance*: chaîne contrôlant l’analyse des tendances, contenant soit
    * « AVG » : définir le composant de tendance en moyenne des séries [par défaut]
    * « None » : aucune tendance, ignorer l’extraction de ce composant
    * « linefit » : extraction du composant de tendance à l’aide de la régression linéaire
* *Test_points*: 0 [Default] ou un entier positif qui spécifie le nombre de points à la fin de la série à exclure du processus d’apprentissage (régression). Ce paramètre doit être défini à des fins de prévision
* *AD_method*: chaîne contrôlant la méthode de détection des anomalies sur la série chronologique résiduelle, contenant un des éléments suivants :
    * « ctukey » : [test de la limite de Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) avec une plage de centile personnalisée du dixième au dixième [par défaut]
    * « Tukey » : [test de la limite de Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) avec une plage de centile de 25-75e standard pour plus d’informations sur les séries chronologiques résiduelles, consultez [series_outliers](series-outliersfunction.md)
* *Seasonality_threshold*: seuil du score saisonnier lorsque le caractère *saisonnier* est défini sur détection automatique. Le seuil de score par défaut est `0.6` . Pour plus d’informations, consultez [series_periods_detect](series-periods-detectfunction.md)

**Renvoie**

 La fonction retourne les séries correspondantes suivantes :

* `ad_flag`: Série ternaire contenant (+ 1,-1, 0) marquage/baisse/aucune anomalie, respectivement
* `ad_score`: Score d’anomalie
* `baseline`: Valeur prédite de la série, en fonction de la décomposition

## <a name="the-algorithm"></a>L’algorithme

Cette fonction suit les étapes suivantes :
1. Appelle [series_decompose ()](series-decomposefunction.md) avec les paramètres respectifs pour créer la série de lignes de base et les résidus.
1. Calcule ad_score série en appliquant [series_outliers ()](series-outliersfunction.md) à la méthode de détection d’anomalie choisie sur la série des résidus.
1. Calcule la série d’ad_flag en appliquant le seuil sur la ad_score à marquer/baisser/ne pas anomalie, respectivement.
 
## <a name="examples"></a>Exemples

### <a name="detect-anomalies-in-weekly-seasonality"></a>Détecter les anomalies dans le caractère saisonnier hebdomadaire

Dans l’exemple suivant, générez une série avec un caractère saisonnier hebdomadaire, puis ajoutez des valeurs hors norme. `series_decompose_anomalies`détecte automatiquement le caractère saisonnier et génère une ligne de base qui capture le modèle répétitif. Les valeurs hors norme que vous avez ajoutées peuvent être clairement repérées dans le composant ad_score.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

### <a name="detect-anomalies-in-weekly-seasonality-with-trend"></a>Détecter les anomalies dans le caractère saisonnier hebdomadaire avec tendance

Dans cet exemple, ajoutez une tendance à la série de l’exemple précédent. Tout d’abord, exécutez `series_decompose_anomalies` avec les paramètres par défaut dans lesquels la `avg` valeur par défaut de tendance prend uniquement la moyenne et ne calcule pas la tendance. La ligne de base générée ne contient pas la tendance et est moins exacte, par rapport à l’exemple précédent. Par conséquent, certains des valeurs hors norme que vous avez insérées dans les données ne sont pas détectées en raison de la variance supérieure.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

Ensuite, exécutez le même exemple, mais comme vous attendez une tendance dans la série, spécifiez `linefit` dans le paramètre Trend. Vous pouvez voir que la ligne de base est plus proche de la série d’entrée. Tous les valeurs hors norme insérées sont détectées, ainsi que des faux positifs. Consultez l’exemple suivant sur la modification du seuil.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

### <a name="tweak-the-anomaly-detection-threshold"></a>Ajuster le seuil de détection des anomalies

Quelques points bruyants ont été détectés comme des anomalies dans l’exemple précédent. Maintenant, augmentez le seuil de détection des anomalies d’une valeur par défaut de 1,5 à 2,5. Utilisez cette plage intercentile, afin que seules les anomalies plus fortes soient détectées. À présent, seuls les valeurs hors norme que vous avez insérées dans les données seront détectées.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
