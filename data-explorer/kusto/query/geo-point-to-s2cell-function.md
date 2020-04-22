---
title: geo_point_to_s2cell() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit geo_point_to_s2cell() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 253b850da519aceb0ead9456f7e49d6dd37d78ec
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663734"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

Calcule la valeur de la chaîne de jetons de cellules S2 pour un emplacement géographique.

Pour plus d’informations sur les cellules S2, cliquez [ici](http://s2geometry.io/devguide/s2cell_hierarchy).

**Syntaxe**

`geo_point_to_s2cell(`*longitude*`, `*latitude*`, `*niveau* de latitude de longitude`)`

**Arguments**

* *longitude*: Valeur longitude d’un emplacement géographique. Longitude x sera considéré comme valide si x est un nombre réel et x est à portée [-180, 180 euros]. 
* *latitude*: Valeur latitude d’une situation géographique. Latitude y sera considéré comme valide si vous êtes un nombre réel et y dans la gamme [-90, 90]. 
* *niveau*: `int` Une option qui définit le niveau de cellule demandé. Les valeurs soutenues sont dans la fourchette [0,30]. Si elle n’est pas `11` précisée, la valeur par défaut est utilisée.

**Retourne**

La valeur de chaîne S2 Cell Token d’un emplacement géographique donné. Si la coordonnées ou le niveau sont invalides, la requête produira un résultat vide.

> [!NOTE]
>
> * S2Cell peut être un outil de clustering géospatial utile.
> * S2Cell a 31 niveaux de hiérarchie avec une couverture de zone allant de 85 011 012,19 km2 au plus haut niveau 0 à 00,44 cm2 au niveau le plus bas 30.
> * S2Cell préserve bien le centre cellulaire pendant l’augmentation du niveau de 0 à 30.
> * S2Cell est une cellule sur une surface de sphère et ses bords sont géodésiques.
> * Invoquer la fonction [geo_s2cell_to_central_point()](geo-s2cell-to-central-point-function.md) sur une chaîne symbolique s2cell qui a été calculée sur la longitude x et la latitude y ne retournera pas nécessairement x et y.
> * Il est possible que deux emplacements géographiques soient très proches l’un de l’autre, mais qu’ils aient des jetons S2 Cell différents.

**S2 Cell couverture approximative de la zone par valeur de niveau**

Pour chaque niveau, la taille de la s2cell est similaire, mais pas exactement égale. La taille des cellules à proximité a tendance à être plus égale.

|Level|Longueur minimale aléatoire de bord de cellules (ROYAUME-Uni)|Longueur aléatoire maximale de bord de cellules (US)|
|--|--|--|
|0|7842 km|7842 km|
|1|3921 km|5004 km|
|2|1825 km|2489 km|
|3|840 km|1310 km|
|4|432 km|636 km|
|5|210 km|315 km|
|6|108 km|156 km|
|7|54 km|78 km|
|8|27 km|39 km|
|9|14 km|20 km|
|10|7 km|10 km|
|11|3 km|5 km|
|12|1699 m|2 km|
|13|850 m|1225 m|
|14|425 m|613 m|
|15|212 m|306 m|
|16|106 m|153 m|
|17|53 m|77 m|
|18|27 m|38 m|
|19|13 m|19 m|
|20|7 m|10 m|
|21|3 m|5 m|
|22|166 cm|2 m|
|23|83 cm|120 cm|
|24|41 cm|60 cm|
|25|21 cm|30 cm|
|26|10 cm|15 cm|
|27|5 cm|7 cm|
|28|2 cm|4 cm|
|29|12 mm|18 mm|
|30|6 mm|9 mm|

La source de la table peut être trouvée [ici](http://s2geometry.io/resources/s2cell_statistics).

Voir aussi [geo_point_to_geohash()](geo-point-to-geohash-function.md).

**Exemples**

Les tempêtes américaines agrégées par s2cell.

:::image type="content" source="images/queries/geo/s2cell.png" alt-text="S2cell des États-Unis":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_s2cell(BeginLon, BeginLat, 5)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print s2cell = geo_point_to_s2cell(-80.195829, 25.802215, 8)
```

| s2cell |
|--------|
| 88d9b  |

L’exemple suivant trouve des groupes de coordonnées. Chaque paire de coordonnées du groupe réside en s2cell avec une superficie maximale de 1632,45 km2.
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", 10.1234, 53,
  "B", 10.3579, 53,
  "C", 10.6842, 53,
]
| summarize count = count(),                                        // items per group count
            locations = make_list(location_id)                      // items in the group
            by s2cell = geo_point_to_s2cell(longitude, latitude, 8) // s2 cell of the group
```

| s2cell | count | locations |
|--------|-------|-----------|
| 47b1d  | 2     | ["A","B"] |
| 47ae3  | 1     | ["C"]     |

L’exemple suivant produit un résultat vide en raison de l’entrée de coordonnées invalide.
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2cell |
|--------|
|        |

L’exemple suivant produit un résultat vide en raison de l’entrée de niveau invalide.
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2cell |
|--------|
|        |

L’exemple suivant produit un résultat vide en raison de l’entrée de niveau invalide.
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2cell |
|--------|
|        |