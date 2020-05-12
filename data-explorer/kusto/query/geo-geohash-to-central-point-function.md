---
title: geo_geohash_to_central_point ()-Azure Explorateur de données
description: Cet article décrit geo_geohash_to_central_point () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: eb59eae0bc014c6ce9060d65f6c3aced80e4275c
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227126"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

Calcule les coordonnées géospatiales qui représentent le centre d’une zone rectangulaire géohash.

En savoir plus sur [`geohash`](https://en.wikipedia.org/wiki/Geohash) .  

**Syntaxe**

`geo_geohash_to_central_point(`*géohachage*`)`

**Arguments**

*géohash*: valeur de chaîne de géohachage telle qu’elle a été calculée par [geo_point_to_geohash ()](geo-point-to-geohash-function.md). La chaîne de hachage peut comporter entre 1 et 18 caractères.

**Retourne**

Valeurs de coordonnées géospatiales au [format géojson](https://tools.ietf.org/html/rfc7946) et d’un type de données [dynamique](./scalar-data-types/dynamic.md) . Si le géohachage n’est pas valide, la requête produira un résultat NULL.

> [!NOTE]
> Le format géojson spécifie les premières longitude et Latitude second.

**Exemples**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|coordonnées|longitude|latitude|
|---|---|---|---|
|{<br>  "type" : "point",<br>  « Coordonnées » : [<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

L’exemple suivant retourne un résultat NULL en raison de l’entrée de géohachage non valide.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_geohash_to_central_point("a")
```

|géohachage|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>Exemple : création de liens d’emplacement détaillés pour Bing Maps

Vous pouvez utiliser la valeur de géohachage pour créer une URL de lien profond vers Bing Maps en pointant sur le point central de géohachage :

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|géohachage|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|
