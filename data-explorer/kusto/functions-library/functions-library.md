---
title: Bibliothèque de fonctions-Azure Explorateur de données
description: Cet article décrit les fonctions définies par l’utilisateur qui étendent les fonctionnalités d’Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 66f8da1655fada7429fcd3087810904bd184546c
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614514"
---
# <a name="functions-library"></a>Bibliothèque de fonctions

L’article suivant contient une liste classée de fonctions définies par l’utilisateur.

## <a name="machine-learning-functions"></a>Fonctions machine learning

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[predict_lf ()](predict-lf.md)|Prédiction à l’aide d’un modèle de Machine Learning formé existant. |
|[predict_onnx_lf ()](predict-onnx-lf.md)| Prédiction à l’aide d’un modèle de Machine Learning formé existant au format ONNX. |

## <a name="series-processing-functions"></a>Fonctions de traitement des séries

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_lf ()](quantize-lf.md)|Quantifier les colonnes métriques. |
|[series_fit_poly_lf ()](series-fit-poly-lf.md)|Ajuster un polynomial à la série à l’aide de l’analyse de régression. |
|[series_moving_avg_lf ()](series-moving-avg-lf.md)|Applique un filtre de moyenne mobile sur une série. |
|[series_rolling_lf ()](series-rolling-lf.md)|Applique une fonction d’agrégation propagée sur une série. |
