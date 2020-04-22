---
title: geo_distance_2points() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit geo_distance_2points() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 30a26bbcc57ecde2ac3beeeb1483b953a0cc5820
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030148"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

Calcule la distance la plus courte entre deux coordonnées géospatiales sur Terre.

**Syntaxe**

`geo_distance_2points(`*p1_longitude**p1_latitude p2_longitude*`, `*p2_longitude*`, `*p2_latitude p2_longitude* `, ``)`

**Arguments**

* *p1_longitude*: Première coordonnées géospatiales, valeur de longitude en degrés. La valeur valide est un nombre réel et dans la gamme [-180, 180 euros].
* *p1_latitude*: Première coordonnées géospatiales, valeur de latitude en degrés. La valeur valide est un nombre réel et dans la gamme [-90, 90 euros].
* *p2_longitude*: Deuxième coordonnées géospatiales, valeur de longitude en degrés. La valeur valide est un nombre réel et dans la gamme [-180, 180 euros].
* *p2_latitude*: Deuxième coordonnées géospatiales, valeur de latitude en degrés. La valeur valide est un nombre réel et dans la gamme [-90, 90 euros].

**Retourne**

La distance la plus courte, en mètres, entre deux emplacements géographiques sur Terre. Si les coordonnées sont invalides, la requête produira un résultat nul.

> [!NOTE]
> * Les coordonnées géospatiales sont interprétées comme représentées par le système de référence de coordonnées [WGS-84.](https://earth-info.nga.mil/GandG/update/index.php?action=home)
> * Le [datum géodésique](https://en.wikipedia.org/wiki/Geodetic_datum) utilisé pour mesurer la distance sur Terre est une sphère.

**Exemples**

L’exemple suivant trouve la distance la plus courte entre Seattle et Los Angeles.


:::image type="content" source="images/geo-distance-2points-function/distance_2points_seattle_los_angeles.png" alt-text="Distance entre Seattle et Los Angeles":::

```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754.35197381   |

Voici une approximation du chemin le plus court de Seattle à Londres. La ligne se compose de coordonnées le long de la LineString et à moins de 500 mètres de celui-ci.

:::image type="content" source="images/geo-distance-2points-function/line_seattle_london.png" alt-text="Seattle à Londres LineString":::

```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

L’exemple suivant trouve toutes les lignes dans lesquelles la distance la plus courte entre deux coordonnées se situe entre 1 et 11 mètres.
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

L’exemple suivant renvoie un résultat nul en raison de l’entrée de coordonnées invalide.
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |