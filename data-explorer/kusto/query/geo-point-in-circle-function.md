---
title: geo_point_in_circle ()-Azure Explorateur de données
description: Cet article décrit geo_point_in_circle () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 6e6ef40fcdeb4942dc0924c86862ee8f6222ac12
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227158"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

Calcule si les coordonnées géospatiales se trouvent à l’intérieur d’un cercle sur la terre.

**Syntaxe**

`geo_point_in_circle(`*p_longitude* `, ` *p_latitude* `, ` *pc_longitude* `, ` *pc_latitude* `, ` *c_radius*`)`

**Arguments**

* *p_longitude*: valeur de longitude de la coordonnée géographique en degrés. La valeur valide est un nombre réel et dans la plage [-180, + 180].
* *p_latitude*: valeur de latitude de la coordonnée géographique en degrés. La valeur valide est un nombre réel et dans la plage [-90, + 90].
* *pc_longitude*: valeur de longitude de la coordonnée géographique du centre du cercle, en degrés. La valeur valide est un nombre réel et dans la plage [-180, + 180].
* *pc_latitude*: valeur de latitude de la coordonnée géographique du centre du cercle, en degrés. La valeur valide est un nombre réel et dans la plage [-90, + 90].
* *c_radius*: rayon du cercle en mètres. La valeur valide doit être positive.

**Retourne**

Indique si les coordonnées géospatiales sont à l’intérieur d’un cercle. Si les coordonnées ou le cercle ne sont pas valides, la requête produira un résultat NULL.

> [!NOTE]
>* Les coordonnées géospatiales sont interprétées comme étant représentées par le système de référence de coordonnées [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) .
>* La [référence géodésique](https://en.wikipedia.org/wiki/Geodetic_datum) utilisée pour mesurer la distance sur la terre est une sphère.
>* Un cercle est un capuchon sphérique sur la terre. Le rayon de l’extrémité est mesuré le long de la surface de la sphère.

**Exemples**

La requête suivante recherche tous les emplacements dans la zone définie par le cercle suivant : rayon de 18 kilomètres, centre aux coordonnées [-122,317404, 47,609119].

:::image type="content" source="images/geo-point-in-circle-function/circle-seattle.png" alt-text="Places près de Seattle":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(longitude:real, latitude:real, place:string)
[
    real(-122.317404), 47.609119, 'Seattle',                   // In circle 
    real(-123.497688), 47.458098, 'Olympic National Forest',   // In exterior of circle  
    real(-122.201741), 47.677084, 'Kirkland',                  // In circle
    real(-122.443663), 47.247092, 'Tacoma',                    // In exterior of circle
    real(-122.121975), 47.671345, 'Redmond',                   // In circle
]
| where geo_point_in_circle(longitude, latitude, -122.317404, 47.609119, 18000)
| project place
```

|place|
|---|
|Seattle|
|Kirkland|
|Redmond|

Événements Storm à Orlando. Les événements sont filtrés par 100 km dans les coordonnées de Orlando et agrégés par type d’événement et hachage.

:::image type="content" source="images/geo-point-in-circle-function/orlando-storm-events.png" alt-text="Événements Storm à Orlando":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

L’exemple suivant montre des captures de taxi de NY dans les 10 mètres d’un emplacement particulier. Les Picks pertinents sont regroupés par hachage.

:::image type="content" source="images/geo-point-in-circle-function/circle-junction.png" alt-text="NY-autres taxis à proximité":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

L’exemple suivant retourne la valeur true.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

L’exemple suivant retourne la valeur false.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

L’exemple suivant retourne un résultat NULL en raison de l’entrée de coordonnée non valide.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

L’exemple suivant retourne un résultat NULL en raison de l’entrée RADIUS de cercle non valide.

```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||
