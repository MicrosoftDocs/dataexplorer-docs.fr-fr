---
title: geo_point_to_geohash() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit geo_point_to_geohash() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: b69d22c56cef4b54a99aa9aa3e9897a2ef177834
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030109"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

Calcule la valeur de la chaîne Geohash pour une emplacement géographique.

Pour plus d’informations sur Geohash, voir [ici](https://en.wikipedia.org/wiki/Geohash).  

**Syntaxe**

`geo_point_to_geohash(`*latitude*`, `*de*`, `longitude [*précision*]`)`

**Arguments**

* *longitude*: Valeur longitude d’un emplacement géographique. Longitude x sera considéré comme valide si x est un nombre réel et est dans la gamme [-180, 180]. 
* *latitude*: Valeur latitude d’une situation géographique. Latitude y sera considéré comme valide si y est un nombre réel et y est dans la gamme [-90, 90]. 
* *précision*: `int` Une option qui définit l’exactitude demandée. Les valeurs soutenues sont dans la fourchette [1,18]. Si elle n’est pas `5` précisée, la valeur par défaut est utilisée.

**Retourne**

La valeur de la chaîne Geohash d’un emplacement géographique donné avec la longueur de précision demandée. Si la coordonnées ou la précision sont invalides, la requête produira un résultat vide.

> [!NOTE]
>
> * Geohash peut être un outil de clustering géospatial utile.
> * Geohash a 18 niveaux de précision avec une couverture de surface allant de 25 millions de km2 au niveau le plus élevé 1 à 0,6 '2 au niveau le plus bas 18.
> * Le préfixe commun des Geohashes indique à proximité des points les uns aux autres. Plus un préfixe partagé est long, plus les deux endroits sont proches. La valeur de précision se traduit par la longueur de la géohash.
> * Geohash est une zone rectangulaire sur une surface de l’avion.
> * Invoquer la fonction [geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md) sur une chaîne de géohash qui a été calculée sur la longitude x et la latitude y ne retournera pas nécessairement x et y.
> * En raison de la définition Geohash, il est possible que deux emplacements géographiques sont très proches l’un de l’autre, mais ont différents codes Geohash.

**Couverture rectangulaire de zone de geohash par valeur de précision :**

| Précision | Largeur     | Hauteur    |
|----------|-----------|-----------|
| 1        | 5000 km   | 5000 km   |
| 2        | 1250 km   | 625 km    |
| 3        | 156,25 km | 156,25 km |
| 4        | 39,06 km  | 19,53 km  |
| 5        | 4,88 km   | 4,88 km   |
| 6        | 1,22 km   | 0,61 km   |
| 7        | 152,59 m  | 152,59 m  |
| 8        | 38,15 m   | 19,07 m   |
| 9        | 4,77 m    | 4,77 m    |
| 10       | 1,19 m    | 0,59 m    |
| 11       | 149,01 mm | 149,01 mm |
| 12       | 37,25 mm  | 18,63 mm  |
| 13       | 4,66 mm   | 4,66 mm   |
| 14       | 1,16 mm   | 0,58 mm   |
| 15       | 145,52 euros  | 145,52 euros  |
| 16       | 36,28 euros   | 18.19   |
| 17       | 4,55 euros    | 4,55 euros    |
| 18       | 1,14    | 0,57 euros    |

Voir aussi [geo_point_to_s2cell()](geo-point-to-s2cell-function.md).

**Exemples**

Les tempêtes américaines agrégées par géohash.

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="Géohash des États-Unis":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| geohash geohash      |
|--------------|
| xn76m27ty9g4 |

```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|geohash geohash|
|---|
|dhwfz15h|

L’exemple suivant trouve des groupes de coordonnées. Chaque paire de coordonnées du groupe réside dans une zone rectangulaire de 4,88 km sur 4,88 km.
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", double(-122.303404), 47.570482,
  "B", double(-122.304745), 47.567052,
  "C", double(-122.278156), 47.566936,
]
| summarize count = count(),                                          // items per group count
            locations = make_list(location_id)                        // items in the group
            by geohash = geo_point_to_geohash(longitude, latitude)    // geohash of the group
```

| geohash geohash | count | locations  |
|---------|-------|------------|
| c23n8   | 2     | ["A", "B"] |
| c23n9   | 1     | ["C"]      |

L’exemple suivant produit un résultat vide en raison de l’entrée de coordonnées invalide.
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| geohash geohash |
|---------|
|         |

L’exemple suivant produit un résultat vide en raison de l’entrée de précision invalide.
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| geohash geohash |
|---------|
|         |