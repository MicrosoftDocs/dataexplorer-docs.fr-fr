---
title: Bibliothèque de fonctions-Azure Explorateur de données
description: Cet article décrit les fonctions définies par l’utilisateur qui étendent les fonctionnalités d’Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: fb08edf4105c44a6be96cf6b2f314cf25887e69a
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96443994"
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
