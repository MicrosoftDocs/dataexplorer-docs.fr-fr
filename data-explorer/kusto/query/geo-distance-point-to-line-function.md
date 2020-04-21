---
title: geo_distance_point_to_line() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit geo_distance_point_to_line() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: c26077efe1eb86683f79b8aaf7d37da579a25edf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514669"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

Calcule la distance la plus courte entre une coordonnées et une ligne sur Terre.

**Syntaxe**

`geo_distance_point_to_line(`*ligne*`, `*de latitude*`, `de*longitudeString*`)`

**Arguments**

* *longitude*: Géospatial coordonne la valeur de longitude en degrés. La valeur valide est un nombre réel et dans la gamme [-180, 180 euros].
* *latitude*: Valeur de latitude de coordonnées géospatiales en degrés. La valeur valide est un nombre réel et dans la gamme [-90, 90 euros].
* *lineString*: Ligne dans le [format GeoJSON](https://tools.ietf.org/html/rfc7946) et d’un type de données [dynamique.](./scalar-data-types/dynamic.md)

**Retourne**

La distance la plus courte, en mètres, entre une coordonnées et une ligne sur Terre. Si la coordonnées ou la ligneString sont invalides, la requête produira un résultat nul.

> [!NOTE]
> * Les coordonnées géospatiales sont interprétées comme représentées par le système de référence de coordonnées [WGS-84.](https://earth-info.nga.mil/GandG/update/index.php?action=home)
> * Le [datum géodésique](https://en.wikipedia.org/wiki/Geodetic_datum) utilisé pour mesurer la distance sur Terre est une sphère. Les bords de ligne sont géodésiques sur la sphère.

**Définition et contraintes LineString**

dynamique («type»: "LineString",coordinates": [lng_1,lat_1], [lng_2,lat_2] ,..., [lng_N,lat_N] ]

* Le tableau de coordonnées LineString doit contenir au moins deux entrées.
* Les coordonnées [longitude,latitude] doivent être valides lorsque la longitude est un nombre réel dans la gamme [-180, 180] et la latitude est un nombre réel dans la gamme [-90, 90 euros].
* La longueur des bords doit être inférieure à 180 degrés. Le bord le plus court entre les deux vertices sera choisi.

> [!TIP]
> Pour de meilleures performances, utilisez des lignes littérales.

**Exemples**

L’exemple suivant trouve la distance la plus courte entre l’aéroport de North Las Vegas et une route à proximité.
![Distance entre l’aéroport de North Las Vegas et la route](./images/queries/geo/distance_point_to_line.png)
```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

Événements de tempête sur la côte sud des États-Unis. Les événements sont filtrés par une distance maximale de 5 km de la ligne de rivage définie.
![Événements de tempête dans la côte sud des États-Unis](./images/queries/geo/us_south_coast_storm_events.png)
```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

Ramassages de taxi NY. Les ramassages sont filtrés à une distance maximale de 0,1 m de la ligne définie.
![Événements de tempête dans la côte sud des États-Unis](./images/queries/geo/park_ave_ny_road.png)
```
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

L’exemple suivant retournera un résultat nul en raison de l’entrée linéaire invalide.
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

L’exemple suivant retournera un résultat nul en raison de l’entrée de coordonnées invalide.
```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |