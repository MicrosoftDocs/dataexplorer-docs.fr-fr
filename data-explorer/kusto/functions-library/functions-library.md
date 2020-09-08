---
title: Bibliothèque de fonctions-Azure Explorateur de données
description: Cet article décrit les fonctions définies par l’utilisateur (UDF) qui étendent les fonctionnalités d’Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 9089722123d1516970df54f0d0fb0fcba3d881e5
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557862"
---
# <a name="functions-library"></a>Bibliothèque de fonctions

L’article suivant contient une liste classée de fonctions définies par l’utilisateur.

## <a name="series-processing-functions"></a>Fonctions de traitement des séries

|Nom de fonction     |Description                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_udf ()](quantize-udf.md)|Quantifier les colonnes métriques. |
|[series_fit_poly_udf ()](series-fit-poly-udf.md)|Ajuster un polynomial à la série à l’aide de l’analyse de régression. |
|[series_moving_avg_udf ()](series-moving-avg-udf.md)|Applique un filtre de moyenne mobile sur une série. |
|[series_rolling_udf ()](series-rolling-udf.md)|Applique une fonction d’agrégation propagée sur une série. |
