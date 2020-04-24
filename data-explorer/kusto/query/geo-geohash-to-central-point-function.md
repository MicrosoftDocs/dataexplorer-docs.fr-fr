---
title: geo_geohash_to_central_point() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit geo_geohash_to_central_point() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: a71265b58cffcec2fcf9d6ac8d412c8dd44866f4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514635"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

Calcule les coordonnées géospatiales qui représentent le centre d’une zone rectangulaire Geohash.

Pour plus d’informations sur Geohash, voir [Wikipédia](https://en.wikipedia.org/wiki/Geohash).  

**Syntaxe**

`geo_geohash_to_central_point(`*geohash geohash*`)`

**Arguments**

*geohash*: Valeur de chaîne Geohash telle qu’elle a été calculée par [geo_point_to_geohash).](geo-point-to-geohash-function.md) La chaîne de géohash peut être de 1 à 18 caractères.

**Retourne**

Les valeurs de coordonnées géospatiales dans [le format GeoJSON](https://tools.ietf.org/html/rfc7946) et d’un type de données [dynamique.](./scalar-data-types/dynamic.md) Si la géohash est invalide, la requête produira un résultat nul.

> [!NOTE]
> Le format GeoJSON spécifie la longitude en premier et la latitude en second lieu.

**Exemples**

```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|coordonnées|longitude|latitude|
|---|---|---|---|
|{<br>  "type": "Point",<br>  "coordonnées": [<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

L’exemple suivant renvoie un résultat nul en raison de l’entrée de géohash invalide.

```kusto
print geohash = geo_geohash_to_central_point("a")
```

|geohash geohash|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>Exemple : Création de liens profonds pour Bing Maps

Vous pouvez utiliser la valeur Geohash pour créer une URL à lien profond vers Bing Maps en pointant vers le point central Geohash :

```kusto
// Use string concatenation to create Bing Map deep-link URL from a geo-point
let point_to_map_url = (_point:dynamic, _title:string) 
{
    strcat('https://www.bing.com/maps?sp=point.', _point.coordinates[1] ,'_', _point.coordinates[0], '_', url_encode(_title)) 
};
// Convert geohash to center point, and then use 'point_to_map_url' to create Bing Map deep-link
let geohash_to_map_url = (_geohash:string, _title:string)
{
    point_to_map_url(geo_geohash_to_central_point(_geohash), _title)
};
print geohash = 'sv8wzvy7'
| extend url = geohash_to_map_url(geohash, "You are here")
```

|geohash geohash|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|