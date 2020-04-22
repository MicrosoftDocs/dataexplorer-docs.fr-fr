---
title: series_decompose_anomalies() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_decompose_anomalies () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 7d9ca0585b40a97d21690f908a50de5be493c412
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663626"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

Détection d’anomalies basée sur la décomposition des séries (se référer à [series_decompose))](series-decomposefunction.md)) 

Prend une expression contenant une série (tableau numérique dynamique) comme entrée et extraire des points anormaux avec des scores.

**Syntaxe**

`series_decompose_anomalies (`*Série* `[, ` *Seuil* `,` *Seasonality* `,` *Trend* `, ` *Test_points* `, ` *AD_method* `,` *Seasonality_threshold*`])`

**Arguments**

* *Série*: Cellule de tableau dynamique qui est un éventail de valeurs numériques, généralement la sortie résultante des opérateurs [de série](make-seriesoperator.md) ou [de make_list](makelist-aggfunction.md)
* *Seuil*: Seuil d’anomalie, défaut 1.5 (valeur k) pour détecter les anomalies légères ou plus fortes
* *Saisonnalité*: Un integer contrôlant l’analyse saisonnière, contenant soit
    * -1: saisonnalité autodétec (en utilisant [series_periods_detect](series-periods-detectfunction.md)) [par défaut] 
    * 0: pas de saisonnalité (c.-à-d. sauter l’extraction de ce composant)
    * période : integer positif, précisant la période prévue dans le nombre d’unités de bacs. Par exemple, si la série est dans 1h poubelles, une période hebdomadaire est de 168 bacs
* *Tendance*: Une chaîne contrôlant l’analyse de tendance, contenant soit    
    * "avg": définir la composante tendance comme moyenne de la série [par défaut]
    * "aucun": pas de tendance, sauter l’extraction de ce composant 
    * "linefit": extraire le composant de tendance à l’aide de la régression linéaire
* *Test_points*: 0 [par défaut] ou integer positif, précisant le nombre de points à la fin de la série à exclure du processus d’apprentissage (régression). Ce paramètre doit être fixé à l’objectif de prévision
* *AD_method*: Une chaîne contrôlant la méthode de détection d’anomalies (voir [series_outliers)](series-outliersfunction.md)sur la série temporelle résiduelle, contenant soit    
    * "ctukey": [Test de clôture Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) avec la gamme personnalisée 10th-90th percentile [par défaut]
    * "tukey": [Test de clôture tukey](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) avec la gamme standard 25e-75e percentile
* *Seasonality_threshold*: Le seuil de la saisonnalité lorsque *la saisonnalité* est réglée pour `0.6` s’autodétection, le seuil de score par défaut est (pour plus de détails voir: [series_periods_detect](series-periods-detectfunction.md))


**Retour**

 La fonction renvoie la série respective suivante :

* `ad_flag`: une série ternaire contenant respectivement une 1, -1, 0) marquant une anomalie de haut en bas/pas
* `ad_score`: score d’anomalie
* `baseline`: la valeur prévue de la série selon la décomposition

**En savoir plus sur l’algorithme**

Cette fonction suit ces étapes :
1. Appels [series_decompose()](series-decomposefunction.md) avec les paramètres respectifs pour créer la série de base et de résidus
2. Calcule ad_score série en appliquant [series_outliers()](series-outliersfunction.md) avec la méthode de détection des anomalies choisie sur la série résiduelle
3. Calcule la série ad_flag en appliquant le seuil sur le ad_score pour marquer respectivement la hausse/baisse/l’absence d’anomalie
 
**Exemples**

**1. Détecter les anomalies dans la saisonnalité hebdomadaire**

Dans l’exemple suivant, nous générons une série avec une saisonnalité hebdomadaire, nous y ajoutons ensuite quelques valeurs aberrantes. `series_decompose_anomalies`détecte automatiquement la saisonnalité et génère une ligne de base qui capture le modèle répétitif. Les valeurs aberrantes que nous avons ajoutées peuvent être clairement repérées dans le composant ad_score.

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

:::image type="content" source="images/samples/series-decompose-anomalies1.png" alt-text="Anomalies de décomposition de série 1":::

**2. Détecter les anomalies dans la saisonnalité hebdomadaire avec tendance**

Dans cet exemple, nous ajoutons une tendance à la série de l’exemple précédent. Tout d’abord, nous courons `series_decompose_anomalies` avec `avg` les paramètres par défaut dans lesquels la valeur par défaut de tendance ne prend que la moyenne et ne calcule pas la tendance, nous pouvons voir que la ligne de base générée ne contient pas la tendance et est moins précis par rapport à l’exemple précédent, par conséquent, certaines des valeurs aberrantes que nous avons inséré dans les données ne sont pas détectés en raison de la variance plus élevée.

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
:::image type="content" source="images/samples/series-decompose-anomalies2.png" alt-text="Anomalies de décomposition de série 2":::

Ensuite, nous jouons le même exemple, mais puisque `linefit` nous nous attendons à une tendance dans la série, nous spécifions dans le paramètre de tendance. Nous pouvons voir que la ligne de base est beaucoup plus proche de la série d’entrées. Toutes les valeurs aberrantes que nous avons insérées sont détectées, ainsi que quelques faux positifs (voir l’exemple suivant sur l’accordage du seuil).

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

:::image type="content" source="images/samples/series-decompose-anomalies3.png" alt-text="Anomalies de décomposition de série 3":::

**3. Modifier le seuil de détection des anomalies**

Dans l’exemple précédent, quelques points bruyants ont été détectés comme anomalies, dans cet exemple, nous augmentons le seuil de détection des anomalies d’un défaut de 1,5 à 2,5 la plage interpercentile de sorte que seules des anomalies plus fortes sont détectées. Nous pouvons voir que maintenant seules les valeurs aberrantes que nous avons insérées dans les données sont détectées.

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

:::image type="content" source="images/samples/series-decompose-anomalies4.png" alt-text="Anomalies de décomposition de série 4":::
