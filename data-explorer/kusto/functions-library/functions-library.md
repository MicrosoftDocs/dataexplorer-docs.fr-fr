---
title: Bibliothèque de fonctions-Azure Explorateur de données
description: Cet article décrit les fonctions définies par l’utilisateur qui étendent les fonctionnalités d’Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 552d55cdf3e40a4138b6521ffa2afdd85eeab68b
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324616"
---
# <a name="functions-library"></a>Bibliothèque de fonctions

L’article suivant contient une liste par catégorie de [fonctions définies par l’utilisateur (UDF)](../query/functions/user-defined-functions.md).

Le code des fonctions définies par l’utilisateur est fourni dans les articles.  Il peut être utilisé dans une instruction Let incorporée dans une requête ou peut être rendu persistant dans une base de données à l’aide de la [fonction. Create](../management/create-function.md).

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
|[series_exp_smoothing_fl ()](series-exp-smoothing-fl.md)|Applique un filtre de lissage exponentiel de base sur une série. |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|Ajuste un polynomial à la série à l’aide de l’analyse de régression. |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|Applique un filtre de moyenne mobile sur une série. |
|[series_rolling_fl()](series-rolling-fl.md)|Applique une fonction d’agrégation propagée sur une série. |
