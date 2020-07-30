---
title: geo_distance_2points ()-Azure Explorateur de données
description: Cet article décrit geo_distance_2points () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 0e8d57e76b0dfa45003f541b54360cb4ac414646
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347882"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

Calcule la distance la plus petite entre deux coordonnées géospatiales sur la terre.

## <a name="syntax"></a>Syntaxe

`geo_distance_2points(`*p1_longitude* `, ` *p1_latitude* `, ` *p2_longitude* `, ` *p2_latitude*`)`

## <a name="arguments"></a>Arguments

* *p1_longitude*: première coordonnée géographique, valeur de longitude en degrés. La valeur valide est un nombre réel et dans la plage [-180, + 180].
* *p1_latitude*: première coordonnée géographique, valeur de latitude en degrés. La valeur valide est un nombre réel et dans la plage [-90, + 90].
* *p2_longitude*: deuxième coordonnée géographique, valeur de longitude en degrés. La valeur valide est un nombre réel et dans la plage [-180, + 180].
* *p2_latitude*: deuxième coordonnée géographique, valeur de latitude en degrés. La valeur valide est un nombre réel et dans la plage [-90, + 90].

## <a name="returns"></a>Retours

Distance la plus petite, en mètres, entre deux emplacements géographiques sur la terre. Si les coordonnées ne sont pas valides, la requête produira un résultat NULL.

> [!NOTE]
> * Les coordonnées géospatiales sont interprétées comme étant représentées par le système de référence de coordonnées [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) .
> * La [référence géodésique](https://en.wikipedia.org/wiki/Geodetic_datum) utilisée pour mesurer la distance sur la terre est une sphère.

## <a name="examples"></a>Exemples

L’exemple suivant recherche la distance la plus petite entre Seattle et Los Angeles.

:::image type="content" source="images/geo-distance-2points-function/distance_2points_seattle_los_angeles.png" alt-text="Distance entre Seattle et Los Angeles":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754,35197381   |

Voici une approximation du chemin le plus bref de Seattle à Londres. La ligne se compose de coordonnées le long du LineString et de 500 mètres.

:::image type="content" source="images/geo-distance-2points-function/line_seattle_london.png" alt-text="De Seattle à Londres LineString":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

L’exemple suivant recherche toutes les lignes dans lesquelles la distance la plus petite entre deux coordonnées est comprise entre 1 et 11 mètres.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend distance_1_to_11m = geo_distance_2points(BeginLon, BeginLat, EndLon, EndLat)
| where distance_1_to_11m between (1 .. 11)
| project distance_1_to_11m
```

| distance_1_to_11m |
|-------------------|
| 10.5723100154958  |
| 7.92153588248414  |

L’exemple suivant retourne un résultat NULL en raison de l’entrée de coordonnée non valide.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |
