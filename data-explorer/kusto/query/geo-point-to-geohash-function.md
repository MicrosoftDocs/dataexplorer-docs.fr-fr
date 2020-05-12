---
title: geo_point_to_geohash ()-Azure Explorateur de données
description: Cet article décrit geo_point_to_geohash () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: c37789ac490814288c7331f0b1ae86b8b2178d67
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226922"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

Calcule la valeur de la chaîne de hachage pour un emplacement géographique.

En savoir plus sur le [géohachage](https://en.wikipedia.org/wiki/Geohash).  

**Syntaxe**

`geo_point_to_geohash(`*longitude* `, ` *Latitude* `, ` [*précision*]`)`

**Arguments**

* *longitude*: valeur de longitude d’un emplacement géographique. La longitude x sera considérée comme valide si x est un nombre réel et se trouve dans la plage [-180, + 180]. 
* *Latitude*: valeur de latitude d’un emplacement géographique. La latitude y sera considérée comme valide si y est un nombre réel et y dans la plage [-90, + 90]. 
* *précision*: option facultative `int` qui définit la précision demandée. Les valeurs prises en charge sont comprises dans la plage [1, 18]. S’il n’est pas spécifié, la valeur par défaut `5` est utilisée.

**Retourne**

Valeur de la chaîne de hachage d’un emplacement géographique donné avec la longueur de précision demandée. Si la coordonnée ou la précision n’est pas valide, la requête produira un résultat vide.

> [!NOTE]
>
> * Le géohachage peut être un outil de clustering géospatiale utile.
> * Le géohachage a 18 niveaux de précision avec couverture de zone allant de 25 millions km² au niveau le plus élevé de 1 à 0,6 μ ² au niveau le plus bas 18.
> * Les préfixes communs de géohash indiquent la proximité des points. Plus un préfixe partagé est long, plus les deux emplacements sont proches. La valeur de précision se traduit par la longueur de géohachage.
> * Le géohachage est une zone rectangulaire sur une surface plane.
> * L’appel de la fonction [geo_geohash_to_central_point ()](geo-geohash-to-central-point-function.md) sur une chaîne de géohachage qui a été calculée sur les x de longitude et Latitude y ne retourne pas nécessairement x et y.
> * En raison de la définition de géohachage, il est possible que deux emplacements géographiques soient très proches l’un de l’autre, mais qu’ils aient des codes de géohachage différents.

**Couverture de zone rectangulaire géohash par valeur de précision :**

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
| 15       | 145,52 μ  | 145,52 μ  |
| 16       | 36,28 μ   | 18,19 μ   |
| 17       | 4,55 μ    | 4,55 μ    |
| 18       | 1,14 μ    | 0,57 μ    |

Voir aussi [geo_point_to_s2cell ()](geo-point-to-s2cell-function.md).

**Exemples**

Événements Storm US regroupés par géohachage.

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="Géohachage US":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| géohachage      |
|--------------|
| xn76m27ty9g4 |

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|géohachage|
|---|
|dhwfz15h|

L’exemple suivant recherche des groupes de coordonnées. Chaque paire de coordonnées du groupe réside dans une zone rectangulaire de 4,88 km par 4,88 km.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

| géohachage | count | locations  |
|---------|-------|------------|
| c23n8   | 2     | ["A", "B"] |
| c23n9   | 1     | ["C"]      |

L’exemple suivant produit un résultat vide en raison de l’entrée de coordonnée non valide.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| géohachage |
|---------|
|         |

L’exemple suivant produit un résultat vide en raison de l’entrée de précision non valide.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| géohachage |
|---------|
|         |
