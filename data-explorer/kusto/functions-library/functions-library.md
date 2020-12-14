---
title: Bibliothèque de fonctions-Azure Explorateur de données
description: Cet article décrit les fonctions définies par l’utilisateur qui étendent les fonctionnalités d’Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: b7e066133817184664e37aec52a562525afa9504
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388951"
---
# <a name="functions-library"></a>Bibliothèque de fonctions

L’article suivant contient une liste par catégorie de [fonctions définies par l’utilisateur (UDF)](../query/functions/user-defined-functions.md).

Le code des fonctions définies par l’utilisateur est fourni dans les articles.  Il peut être utilisé dans une instruction Let incorporée dans une requête ou peut être rendu persistant dans une base de données à l’aide de [`.create function`](../management/create-function.md) .

## <a name="machine-learning-functions"></a>Fonctions machine learning

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[kmeans_fl()](kmeans-fl.md)|Clusterisation à l’aide de l’algorithme k-signifiant. |
|[predict_fl()](predict-fl.md)|Prédiction à l’aide d’un modèle de Machine Learning formé existant. |
|[predict_onnx_fl()](predict-onnx-fl.md)| Prédiction à l’aide d’un modèle de Machine Learning formé existant au format ONNX. |

## <a name="promql-functions"></a>PromQL, fonctions

La section suivante contient les fonctions [PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/) courantes. Ces fonctions peuvent être utilisées pour l’analyse des métriques gérées par Azure Explorateur de données par le système de surveillance [Prometheus](https://prometheus.io/) . Toutes les fonctions supposent que les métriques dans Azure Explorateur de données sont structurées à l’aide du [modèle de données Prometheus](https://prometheus.io/docs/concepts/data_model/).


|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[series_metric_fl ()](series-metric-fl.md)|Sélectionne et récupère les séries chronologiques stockées avec le modèle de données Prometheus. |
|[series_rate_fl ()](series-rate-fl.md)|Calcule le taux moyen d’augmentation de la métrique du compteur par seconde. |

## <a name="series-processing-functions"></a>Fonctions de traitement des séries

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|Quantifier les colonnes métriques. |
|[series_dot_product_fl()](series-dot-product-fl.md)|Calcule le produit scalaire de deux vecteurs numériques. |
|[series_downsample_fl()](series-downsample-fl.md)|Sous-échantillonne une série chronologique par un facteur entier. |
|[series_exp_smoothing_fl()](series-exp-smoothing-fl.md)|Applique un filtre de lissage exponentiel de base sur une série. |
|[series_fit_lowess_fl()](series-fit-lowess-fl.md)|Ajuste un polynomial local à la série à l’aide de la méthode LOWESS. |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|Ajuste un polynomial à la série à l’aide de l’analyse de régression. |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|Applique un filtre de moyenne mobile sur une série. |
|[series_rolling_fl()](series-rolling-fl.md)|Applique une fonction d’agrégation propagée sur une série. |
