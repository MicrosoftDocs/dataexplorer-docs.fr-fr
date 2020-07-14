---
title: geo_polygon_densify ()-Azure Explorateur de données
description: Cet article décrit geo_polygon_densify () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 6ef78d3078fc396d4ebfb782e54f31096a1e8777
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280704"
---
# <a name="geo_polygon_densify"></a>geo_polygon_densify ()

Convertit les bords planaires Polygon ou multipolygone en géodésique en ajoutant des points intermédiaires.

**Syntaxe**

`geo_polygon_densify(`*polygone* `, ` *tolérance*`)`

**Arguments**

* *polygone*: polygone ou multipolygone au [format géojson](https://tools.ietf.org/html/rfc7946) et d’un type de données [dynamique](./scalar-data-types/dynamic.md) .
* *Tolerance*: valeur numérique facultative qui définit la distance maximale en mètres entre le bord planaire d’origine et la chaîne d’arêtes géodésique convertie. Les valeurs prises en charge sont comprises dans la plage [0,1, 10000]. S’il n’est pas spécifié, la valeur par défaut `10` est utilisée.

**Retourne**

Densified Polygon au [format géojson](https://tools.ietf.org/html/rfc7946) et d’un type de données [dynamique](./scalar-data-types/dynamic.md) . Si le polygone ou la tolérance n’est pas valide, la requête produira un résultat NULL.

> [!NOTE]
> * Les coordonnées géospatiales sont interprétées comme étant représentées par le système de référence de coordonnées [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) .
> * Le polygone doit être correctement défini, mais la fonction ne vérifie pas la validité du polygone.

**Définition de polygone**

dynamique ({"type" : "Polygon", "coordinates" : [LinearRingShell, LinearRingHole_1,..., LinearRingHole_N]})

dynamique ({"type" : "MultiPolygon", "coordinates" : [[LinearRingShell, LinearRingHole_1,..., LinearRingHole_N],..., [LinearRingShell, LinearRingHole_1,..., LinearRingHole_M]]})

* LinearRingShell est requis et défini en tant que `counterclockwise` tableau ordonné de coordonnées [[lng_1, lat_1],..., [lng_i, lat_i],..., [lng_j, lat_j],..., [lng_1, lat_1]]. Il ne peut y avoir qu’un seul Shell.
* LinearRingHole est facultatif et défini en tant que `clockwise` tableau ordonné de coordonnées [[lng_1, lat_1],..., [lng_i, lat_i],..., [lng_j, lat_j],..., [lng_1, lat_1]]. Il peut y avoir un nombre quelconque d’anneaux intérieurs et de trous.
* Les vertex LinearRing doivent être distincts avec au moins trois coordonnées. La première coordonnée doit être égale à la dernière. Au moins quatre entrées sont requises.
* Les coordonnées [longitude, Latitude] doivent être valides. La longitude doit être un nombre réel compris dans la plage [-180, + 180] et la latitude doit être un nombre réel compris dans la plage [-90, + 90].
* LinearRingShell englobe au plus la moitié de la sphère. LinearRing divise la sphère en deux régions. La plus petite des deux régions sera choisie.
* La longueur du bord LinearRing doit être inférieure à 180 degrés. Le bord le plus petit entre les deux vertex est choisi.

**Contraintes**

* Le nombre maximal de points dans le polygone densified est limité à 10485760.
* Le stockage de polygones dans un format [dynamique](./scalar-data-types/dynamic.md) a des limites de taille.
* Densifying un polygone valide peut l’invalider. L’algorithme ajoute des points de manière non uniforme et, par conséquent, les bords peuvent être entrelacés.

**Motivation**

* Le [format géojson](https://tools.ietf.org/html/rfc7946) définit un bord entre deux points comme une ligne cartésienne droite.
* La décision d’utiliser des arêtes géodésique ou planaires peut dépendre du jeu de données et est particulièrement pertinente dans les bords longs.

**Exemples**

L’exemple suivant densifies Manhattan Central Park Polygon. Les bords sont courts et la distance entre les bords planaires et leurs équivalents géodésique est inférieure à la distance spécifiée par Tolerance. Par conséquent, le résultat reste inchangé.

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[-73.958244,40.800719],[-73.949146,40.79695],[-73.973093,40.764226],[-73.982062,40.768159],[-73.958244,40.800719]]]})))
```

|densified_polygon|
|---|
|{"type" : "Polygon", "coordinates" : [[[-73.958244, 40.800719], [-73.949146, 40.79695], [-73.973093, 40.764226], [-73.982062, 40.768159], [-73.958244, 40.800719]]]}|

L’exemple suivant densifies deux bords du polygone. La longueur des bords densified est ~ 110 km

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]})))
```

|densified_polygon|
|---|
|{"type" : "Polygon", "coordinates" : [[[10, 10], [(10.25, 10], [10.5, 10], [10.75, 10], [11, 10], [11, 11], [10.75, 11], [10,5, 11], [(10.25, 11], [10, 11], [10, 10]]]}|

L’exemple suivant retourne un résultat NULL en raison de l’entrée de coordonnée non valide.

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,900],[11,10],[11,11],[10,11],[10,10]]]}))
```

|densified_polygon|
|---|
||

L’exemple suivant retourne un résultat NULL en raison de l’entrée de tolérance non valide.

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]}), 0)
```

|densified_polygon|
|---|
||
