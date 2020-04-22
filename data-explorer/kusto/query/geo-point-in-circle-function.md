---
title: geo_point_in_circle() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit geo_point_in_circle () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: ca3ca8a1ac2299c43888ac827d46c25d02e4999d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663800"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

Calcule si les coordonnées géospatiales sont à l’intérieur d’un cercle sur Terre.

**Syntaxe**

`geo_point_in_circle(`*p_longitude p_latitude**p_latitude*`, `*pc_longitude*`, `*pc_latitude*`, `*c_radius* `, ``)`

**Arguments**

* *p_longitude*: Géospatial coordonne la valeur de longitude en degrés. La valeur valide est un nombre réel et dans la gamme [-180, 180 euros].
* *p_latitude*: Valeur de latitude de coordonnées géospatiales en degrés. La valeur valide est un nombre réel et dans la gamme [-90, 90 euros].
* *pc_longitude*: Cercle centre géospatial coordonner la valeur de longitude en degrés. La valeur valide est un nombre réel et dans la gamme [-180, 180 euros].
* *pc_latitude*: cercle centre géospatial coordonner la valeur de latitude en degrés. La valeur valide est un nombre réel et dans la gamme [-90, 90 euros].
* *c_radius*: Rayon de cercle en mètres. La valeur valide doit être positive.

**Retourne**

Indique si les coordonnées géospatiales se trouvent à l’intérieur d’un cercle. Si les coordonnées ou le cercle sont invalides, la requête produira un résultat nul.

> [!NOTE]
>* Les coordonnées géospatiales sont interprétées comme représentées par le système de référence de coordonnées [WGS-84.](https://earth-info.nga.mil/GandG/update/index.php?action=home)
>* Le [datum géodésique](https://en.wikipedia.org/wiki/Geodetic_datum) utilisé pour mesurer la distance sur Terre est une sphère.
>* Circle est une casquette sphérique sur Terre. Le rayon du bouchon est mesuré le long de la surface de la sphère.

**Exemples**

La requête suivante trouve tous les endroits dans la zone définie par un cercle avec un rayon de 18 km dont le centre est à [-122.317404, 47.609119] coordonnées.

:::image type="content" source="images/queries/geo/circle_seattle.png" alt-text="Lieux près de Seattle":::

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

Événements de tempête à Orlando. Les événements sont filtrés par les coordonnées d’Orlando, dans un rayon de 100 km et agrégés par type d’événement et de hachage.

:::image type="content" source="images/queries/geo/orlando_storm_events.png" alt-text="Événements de tempête à Orlando":::

```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

L’exemple suivant montre NY Taxi pick-up à proximité d’un certain emplacement et à moins de 10 mètres. Les micros pertinents sont agrégés par hachage.

:::image type="content" source="images/queries/geo/circle_junction.png" alt-text="NY Taxi à proximité Pickups":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

L’exemple suivant reviendra vrai.
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

L’exemple suivant reviendra faux.
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

L’exemple suivant retournera un résultat nul en raison de l’entrée de coordonnées invalide.
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

L’exemple suivant retournera un résultat nul en raison de l’entrée invalide de rayon de cercle.
```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||