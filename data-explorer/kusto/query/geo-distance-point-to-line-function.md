---
title: geo_distance_point_to_line ()-Azure Explorateur de données
description: Cet article décrit geo_distance_point_to_line () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: b4a30aa4285b8f6e22e5d4057fe7d408d548a27b
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280559"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

Calcule la distance la plus petite entre une coordonnée et une ligne sur la terre.

**Syntaxe**

`geo_distance_point_to_line(`*longitude* `, ` *Latitude* `, ` *lineString*`)`

**Arguments**

* *longitude*: valeur de longitude de coordonnée géographique en degrés. La valeur valide est un nombre réel et dans la plage [-180, + 180].
* *Latitude*: valeur de latitude de la coordonnée géographique en degrés. La valeur valide est un nombre réel et dans la plage [-90, + 90].
* *lineString*: ligne au [format géojson](https://tools.ietf.org/html/rfc7946) et d’un type de données [dynamique](./scalar-data-types/dynamic.md) .

**Retourne**

Distance la plus petite, en mètres, entre une coordonnée et une ligne sur terre. Si la coordonnée ou le lineString ne sont pas valides, la requête produira un résultat NULL.

> [!NOTE]
> * Les coordonnées géospatiales sont interprétées comme étant représentées par le système de référence de coordonnées [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) .
> * La [référence géodésique](https://en.wikipedia.org/wiki/Geodetic_datum) utilisée pour mesurer la distance sur la terre est une sphère. Les arêtes de ligne sont des [géodésique](https://en.wikipedia.org/wiki/Geodesic) sur la sphère.
> * Si les bords des lignes d’entrée sont des lignes cartésienles directes, envisagez d’utiliser [geo_line_densify ()](geo-line-densify-function.md) afin de convertir des arêtes planaires en géodésiques.

**Définition et contraintes de LineString**

dynamique ({"type" : "LineString", "coordinates" : [[lng_1, lat_1], [lng_2, lat_2],..., [lng_N, lat_N]]})

* Le tableau de coordonnées LineString doit contenir au moins deux entrées.
* Les coordonnées [longitude, Latitude] doivent être valides, où la longitude est un nombre réel dans la plage [-180, + 180] et la latitude est un nombre réel dans la plage [-90, + 90].
* La longueur du bord doit être inférieure à 180 degrés. Le bord le plus petit entre les deux vertex est choisi.

> [!TIP]
> Pour de meilleures performances, utilisez des lignes littérales.

**Exemples**

L’exemple suivant recherche la distance la plus petite entre l’aéroport du Nord de Las Vegas et la route avoisinante.

:::image type="content" source="images/geo-distance-point-to-line-function/distance-point-to-line.png" alt-text="Distance entre l’aéroport et la route du Nord de Las Vegas":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

Événements Storm dans la côte sud-est des États-Unis. Les événements sont filtrés par une distance maximale de 5 kilomètres par rapport à la ligne Shore définie.

:::image type="content" source="images/geo-distance-point-to-line-function/us-south-coast-storm-events.png" alt-text="Événements Storm dans la côte sud des États-Unis":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

NY. Les Picks sont filtrés par une distance maximale de 0,1 m à partir de la ligne définie.

:::image type="content" source="images/geo-distance-point-to-line-function/park-ave-ny-road.png" alt-text="Événements Storm dans la côte sud des États-Unis":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

L’exemple suivant retourne un résultat NULL en raison de l’entrée LineString non valide.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

L’exemple suivant retourne un résultat NULL en raison de l’entrée de coordonnée non valide.

```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |
