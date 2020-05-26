---
title: Fonctionnalité d’apprentissage automatique dans Azure Data Explorer
description: Utilisez le clustering d’apprentissage automatique pour l’analyse de la cause racine dans Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: abef0650485ac1feb53d43f42559c5a7fdfb75c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374031"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Fonctionnalité d’apprentissage automatique dans Azure Data Explorer

Azure Data Explorer, une plateforme d’analyse des Big Data, est utilisée pour surveiller l’intégrité du service, la qualité de service ou le comportement anormal d’appareils en dysfonctionnement à l’aide de fonctions de [prévision et de détection d’anomalie intégrées](anomaly-detection.md). Si un modèle anormal est détecté, une analyse de la cause racine est effectuée pour atténuer ou résoudre l’anomalie.

Le processus de diagnostic est long et complexe et effectué par des experts du domaine. Le processus comprend l’extraction et la jonction de données supplémentaires issues de sources différentes pour le même délai d’exécution, la recherche de modifications dans la distribution des valeurs sur plusieurs dimensions, la création de graphiques de variables supplémentaires, et d’autres techniques en fonction des connaissances de domaine et de l’intuition. Dans la mesure où ces scénarios de diagnostic sont courants dans Azure Data Explorer, les plug-ins d’apprentissage automatique sont disponibles pour simplifier la phase de diagnostic et raccourcir la durée de l’analyse de la cause racine.

Azure Data Explorer a trois plug-ins de Machine Learning : [`autocluster`](kusto/query/autoclusterplugin.md), [`basket`](kusto/query/basketplugin.md) et [`diffpatterns`](kusto/query/diffpatternsplugin.md). Tous les plug-ins implémentent des algorithmes de clustering. Les plug-ins `autocluster` et `basket` regroupent en cluster un jeu d’enregistrements unique et le plug-in `diffpatterns` regroupe en cluster les différences entre deux jeux.

## <a name="clustering-a-single-record-set"></a>Clustering d’un jeu d’enregistrements unique

Un scénario courant comprend un jeu de données sélectionné selon un critère spécifique, telle qu’une période montrant un comportement anormal, des mesures de températures élevées sur un appareil, des commandes longue durée et les utilisateurs dépensant le plus. Nous aimerions un moyen facile et rapide de rechercher des modèles courants (segments) dans les données. Les modèles sont un sous-ensemble du jeu de données dont les enregistrements partagent les mêmes valeurs sur plusieurs dimensions (colonnes catégorielles). La requête suivante génère et affiche une série chronologique d’exceptions de service sur une semaine dans des emplacements de dix minutes  :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Graphique temporel des exceptions de service](media/machine-learning-clustering/service-exceptions-timechart.png)

Le nombre d’exceptions de service se met en corrélation avec l’ensemble du trafic de service. Vous pouvez voir clairement le modèle quotidien pour les jours ouvrables allant du lundi au vendredi, avec une augmentation du nombre d’exceptions de service à la mi journée et une baisse de ces nombres au cours de la nuit. Ces nombres restent faibles et stables pendant le week-end. Des pics d’exception peuvent être détectés à l’aide de la [détection d’anomalie de série chronologique](anomaly-detection.md#time-series-anomaly-detection) dans Azure Data Explorer.

Le deuxième pic de données se produit le mardi après-midi. La requête suivante est utilisée pour diagnostiquer ce pic de manière plus approfondie. Utilisez la requête pour redessiner le graphique autour du pic dans une résolution plus élevée (huit heures dans des emplacements d’une minute) pour vérifier s’il s’agit d’un pic pointu et afficher ses bordures.

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=) **\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Examiner de manière plus approfondie le graphique temporel du pic](media/machine-learning-clustering/focus-spike-timechart.png)

Nous observons un pic de deux minutes étroit de 15 h 00 à 15 h 02. Dans la requête suivante, comptez les exceptions dans cette fenêtre de deux minutes :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Count |
|---------|
|972    |

Dans la requête suivante, exemple de 20 exceptions sur 972 :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Région | ScaleUnit | DeploymentId                     | Tracepoint | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Utiliser autocluster() pour le clustering d’un jeu d’enregistrements unique

Même s’il existe moins d’un millier d’exceptions, il est toujours difficile de repérer les segments communs dans la mesure où chaque colonne comporte plusieurs valeurs. Vous pouvez utiliser le plug-in [`autocluster()`](kusto/query/autoclusterplugin.md) pour extraire instantanément une petite liste de segments communs et rechercher les clusters intéressants situés dans la période de deux minutes du pic comme indiqué dans la requête suivante :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| ID de segment | Count | Pourcentage | Région | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Vous pouvez voir dans les résultats ci-dessus que le segment le plus dominant contient 65,74 % du total des enregistrements d’exceptions totale et qu’il partage quatre dimensions. Le segment suivant est beaucoup moins courant et contient seulement 9,67 % des enregistrements et partage trois dimensions. Les autres segments sont encore moins courants. 

Autocluster utilise un algorithme propriétaire pour explorer plusieurs dimensions et extraire des segments intéressants. « Intéressant » signifie que chaque segment couvre de manière importante le jeu d’enregistrements et le jeu de fonctionnalités. Les segments sont également divergents, ce qui signifie que chacun d’eux est considérablement différent des autres. Un ou plusieurs de ces segments peuvent être pertinents pour le processus d’analyse de la cause racine. Pour réduire le temps passé à examiner et évaluer les segments, autocluster extrait uniquement une petite liste de segments.

### <a name="use-basket-for-single-record-set-clustering"></a>Utiliser basket() pour le clustering d’un jeu d’enregistrements unique

Vous pouvez également utiliser le plug-in [`basket()`](kusto/query/basketplugin.md) comme indiqué dans la requête suivante :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| ID de segment | Count | Pourcentage | Région | ScaleUnit | DeploymentId | Tracepoint | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Cette requête implémente l’algorithme Apriori pour explorer les jeux d’articles et extrait tous les segments dont la couverture du jeu d’enregistrements est au-dessus d’un seuil (valeur par défaut 5 %). Vous pouvez voir qu’un nombre plus important de segments a été extrait avec des segments similaires (par exemple, les segments 0,1 ou 2,3).

Les deux plug-ins sont puissants et simples d’utilisation. Toutefois, leur gros inconvénient est qu’ils mettent en cluster un jeu d’enregistrements unique de manière non supervisée (sans libellé). Il est donc difficile de savoir si les modèles extraits caractérisent le jeu d’enregistrements sélectionné (enregistrements anormaux) ou le jeu d’enregistrements global.

## <a name="clustering-the-difference-between-two-records-sets"></a>Mettre en cluster la différence entre deux jeux d’enregistrements

Le plug-in [`diffpatterns()`](kusto/query/diffpatternsplugin.md) permet de résoudre la limite de `autocluster` et `basket`. `Diffpatterns` prend deux jeux d’enregistrements et extrait les principaux segments qui diffèrent entre eux. Un jeu contient généralement le jeu d’enregistrements anormal en cours d’étude (un analysé par `autocluster` et `basket`). L’autre contient le jeu d’enregistrements de référence (de base). 

Dans la requête ci-dessous, nous utilisons `diffpatterns` pour rechercher les clusters intéressants situés dans la période de deux minutes du pic, qui diffèrent de ceux des clusters de la ligne de base. Nous définissons la fenêtre de référence comme correspondant aux huit minutes précédant 15 h 00 (début du pic). Nous devons également étendre par une colonne binaire (AB) indiquant si un enregistrement spécifique appartient au jeu de référence ou au jeu anormal. `Diffpatterns` implémente un algorithme d’apprentissage supervisé, où les deux libellés de classe ont été générés selon le rapport entre l’indicateur anormal et l’indicateur de référence (AB).

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| ID de segment | CountA | CountB | PercentA | PercentB | PercentDiffAB | Région | ScaleUnit | DeploymentId | Tracepoint |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28,9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

Le segment plus dominant est le même segment que celui extrait par `autocluster`, sa couverture sur la fenêtre anormale de deux minutes est également de 65,74 %. Toutefois, sa couverture sur la fenêtre de référence de huit minutes est seulement de 1,7 %. La différence est de 64,04 %. Cette différence semble être liée au pic d’activité anormale. Vous pouvez vérifier cette hypothèse en fractionnant le graphique d’origine avec, d’un côté, les enregistrements appartenant à ce segment qui pose problème et, de l’autre, les autres segments comme indiqué dans la requête ci-dessous :

**\[** [**Cliquer pour exécuter la requête**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Valider le graphique temporel de segment ’diffpattern’](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Ce graphique permet de constater que le pic du mardi après-midi est survenu en raison d’exceptions issues de ce segment spécifique, découvertes à l’aide du plug-in `diffpatterns`.

## <a name="summary"></a>Résumé

Les plug-ins de Machine Learning d’Azure Data Explorer sont utiles dans de nombreux scénarios. Les plug-ins `autocluster` et `basket` implémentent un algorithme d’apprentissage non supervisé et sont faciles à utiliser. `Diffpatterns` implémente un algorithme d’apprentissage supervisé et, bien que plus complexe, il est plus puissant pour extraire les segments de différenciation en vue de l’analyse de la cause racine.

Ces plug-ins sont utilisés de manière interactive dans des scénarios ad-hoc et dans des services de surveillance en temps réel quasi automatiques. Dans Azure Data Explorer, la détection d’anomalie de série chronologique est suivie d’un processus de diagnostic hautement optimisé pour répondre aux normes de performance nécessaires.
