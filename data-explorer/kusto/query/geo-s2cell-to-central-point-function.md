---
title: geo_s2cell_to_central_point() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit geo_s2cell_to_central_point() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 624d163b508768d0a5316ee3ec62a12b11942176
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514431"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

Calcule les coordonnées géospatiales qui représentent le centre de S2 Cell.

Pour plus d’informations sur les cellules S2, cliquez [ici](http://s2geometry.io/devguide/s2cell_hierarchy).

**Syntaxe**

`geo_s2cell_to_central_point(`*s2cell*`)`

**Arguments**

*s2cell*: S2 Cell Token string value as it was calculated by [geo_point_to_s2cell()](geo-point-to-s2cell-function.md). La longueur maximale de la chaîne S2 Cell est de 16 caractères.

**Retourne**

Les valeurs de coordonnées géospatiales dans [le format GeoJSON](https://tools.ietf.org/html/rfc7946) et d’un type de données [dynamique.](./scalar-data-types/dynamic.md) Si le jeton de cellule S2 est invalide, la requête produira un résultat nul.

> [!NOTE]
> Le format GeoJSON spécifie la longitude en premier et la latitude en second lieu.

**Exemples**

```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|coordonnées|longitude|latitude|
|---|---|---|---|
|{<br>  "type": "Point",<br>  "coordonnées": [<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

L’exemple suivant renvoie un résultat nul en raison de l’entrée de jetons de cellules S2 invalide.
```kusto
print point = geo_s2cell_to_central_point("a")
```

|point|
|---|
||