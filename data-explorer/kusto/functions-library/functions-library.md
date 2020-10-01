---
title: Bibliothèque de fonctions-Azure Explorateur de données
description: Cet article décrit les fonctions définies par l’utilisateur qui étendent les fonctionnalités d’Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 8cdccf261f755a0ea7a3d6a6299aa54ce021f366
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614972"
---
# <a name="functions-library"></a>Bibliothèque de fonctions

L’article suivant contient une liste par catégorie de [fonctions définies par l’utilisateur (UDF)](../query/functions/user-defined-functions.md).

Le code des fonctions définies par l’utilisateur est fourni dans les articles.  Il peut être utilisé dans une instruction Let incorporée dans une requête ou peut être rendu persistant dans une base de données à l’aide de la [fonction. Create](../management/create-function.md).

## <a name="machine-learning-functions"></a>Fonctions machine learning

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[predict_fl()](predict-fl.md)|Prédiction à l’aide d’un modèle de Machine Learning formé existant. |
|[predict_onnx_fl()](predict-onnx-fl.md)| Prédiction à l’aide d’un modèle de Machine Learning formé existant au format ONNX. |

## <a name="series-processing-functions"></a>Fonctions de traitement des séries

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|Quantifier les colonnes métriques. |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|Ajuster un polynomial à la série à l’aide de l’analyse de régression. |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|Applique un filtre de moyenne mobile sur une série. |
|[series_rolling_fl()](series-rolling-fl.md)|Applique une fonction d’agrégation propagée sur une série. |
