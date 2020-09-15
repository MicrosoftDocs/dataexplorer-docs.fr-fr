---
title: Bibliothèque de fonctions-Azure Explorateur de données
description: Cet article décrit les fonctions définies par l’utilisateur qui étendent les fonctionnalités d’Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 5b3457d52be37d4c0090db2f34c89994bc829a53
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90067536"
---
# <a name="functions-library"></a>Bibliothèque de fonctions

L’article suivant contient une liste classée de fonctions définies par l’utilisateur.

## <a name="machine-learning-functions"></a>Fonctions machine learning

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[predict_fl ()](predict-fl.md)|Prédiction à l’aide d’un modèle de Machine Learning formé existant. |
|[predict_onnx_fl ()](predict-onnx-fl.md)| Prédiction à l’aide d’un modèle de Machine Learning formé existant au format ONNX. |

## <a name="series-processing-functions"></a>Fonctions de traitement des séries

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl ()](quantize-fl.md)|Quantifier les colonnes métriques. |
|[series_fit_poly_fl ()](series-fit-poly-fl.md)|Ajuster un polynomial à la série à l’aide de l’analyse de régression. |
|[series_moving_avg_fl ()](series-moving-avg-fl.md)|Applique un filtre de moyenne mobile sur une série. |
|[series_rolling_fl ()](series-rolling-fl.md)|Applique une fonction d’agrégation propagée sur une série. |
