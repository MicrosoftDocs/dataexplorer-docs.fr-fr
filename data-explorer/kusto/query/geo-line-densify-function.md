---
title: geo_line_densify ()-Azure Explorateur de données
description: Cet article décrit geo_line_densify () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: c5a66255f719d3bd0da962a8eb9d3cae23a8c254
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347831"
---
# <a name="geo_line_densify"></a>geo_line_densify()

Convertit les bords de trait planaires en géodésique en ajoutant des points intermédiaires.

## <a name="syntax"></a>Syntaxe

`geo_line_densify(`*lineString* `, ` *tolérance*`)`

## <a name="arguments"></a>Arguments

* *lineString*: ligne au [format géojson](https://tools.ietf.org/html/rfc7946) et d’un type de données [dynamique](./scalar-data-types/dynamic.md) .
* *Tolerance*: valeur numérique facultative qui définit la distance maximale en mètres entre le bord planaire d’origine et la chaîne d’arêtes géodésique convertie. Les valeurs prises en charge sont comprises dans la plage [0,1, 10000]. S’il n’est pas spécifié, la valeur par défaut `10` est utilisée.

## <a name="returns"></a>Retourne

Densified ligne au [format géojson](https://tools.ietf.org/html/rfc7946) et d’un type de données [dynamique](./scalar-data-types/dynamic.md) . Si la ligne ou la tolérance n’est pas valide, la requête produira un résultat NULL.

> [!NOTE]
> * Les coordonnées géospatiales sont interprétées comme étant représentées par le système de référence de coordonnées [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) .

**Définition de LineString**

dynamique ({"type" : "LineString", "coordinates" : [[lng_1, lat_1], [lng_2, lat_2],..., [lng_N, lat_N]]})

* Le tableau de coordonnées LineString doit contenir au moins deux entrées.
* Les coordonnées [longitude, Latitude] doivent être valides. La longitude doit être un nombre réel dans la plage [-180, + 180] et la latitude doit être un nombre réel dans la plage [-90, + 90].
* La longueur du bord doit être inférieure à 180 degrés. Le bord le plus petit entre les deux vertex est choisi.

**Contraintes**

* Le nombre maximal de points dans la ligne densified est limité à 10485760.
* Le stockage des lignes dans un format [dynamique](./scalar-data-types/dynamic.md) a des limites de taille.

**Motivation**

* Le [format géojson](https://tools.ietf.org/html/rfc7946) définit un bord entre deux points comme une ligne cartésienne droite.
* La décision d’utiliser des arêtes géodésique ou planaires peut dépendre du jeu de données et est particulièrement pertinente dans les bords longs.

## <a name="examples"></a>Exemples

L’exemple suivant densifies une route dans l’île de Manhattan. Le bord est petit et la distance entre l’arête planaire et son équivalent géodésique est inférieure à la distance spécifiée par Tolerance. Par conséquent, le résultat reste inchangé.

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[-73.949247, 40.796860],[-73.973017, 40.764323]]})))
```

|densified_line|
|---|
|{"type" : "LineString", "coordinates" : [[-73,949247, 40,796860], [-73,973017, 40,764323]]}|

L’exemple suivant densifies un bord de ~ 130km length

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[50, 50], [51, 51]]})))
```

|densified_line|
|---|
|{"type" : "LineString", "coordinates" : [[50, 50], [50.125, 50.125], [50.25, 50.25], [50.375, 50.375], [50.5, 50.5], [50.625, 50.625], [50.75, 50.75], [50.875, 50.875], [51, 51]]}|

L’exemple suivant retourne un résultat NULL en raison de l’entrée de coordonnée non valide.

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[300,1],[1,1]]}))
```

|densified_line|
|---|
||

L’exemple suivant retourne un résultat NULL en raison de l’entrée de tolérance non valide.

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[1,1],[2,2]]}), 0)
```

|densified_line|
|---|
||
