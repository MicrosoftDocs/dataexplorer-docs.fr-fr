---
title: geo_s2cell_to_central_point ()-Azure Explorateur de données
description: Cet article décrit geo_s2cell_to_central_point () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 7eabcb3cb0c3fd001290848e73bb534ff8ea4218
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226820"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

Calcule les coordonnées géospatiales qui représentent le centre d’une cellule S2.

En savoir plus sur la [hiérarchie des cellules S2](https://s2geometry.io/devguide/s2cell_hierarchy).

**Syntaxe**

`geo_s2cell_to_central_point(`*s2cell*`)`

**Arguments**

*s2cell*: valeur de chaîne de jeton de cellule S2 telle qu’elle a été calculée par [geo_point_to_s2cell ()](geo-point-to-s2cell-function.md). La longueur de chaîne maximale du jeton de cellule S2 est de 16 caractères.

**Retourne**

Valeurs de coordonnées géospatiales au [format géojson](https://tools.ietf.org/html/rfc7946) et d’un type de données [dynamique](./scalar-data-types/dynamic.md) . Si le jeton de cellule S2 n’est pas valide, la requête produira un résultat NULL.

> [!NOTE]
> Le format géojson spécifie les premières longitude et Latitude second.

**Exemples**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|coordonnées|longitude|latitude|
|---|---|---|---|
|{<br>  "type" : "point",<br>  « Coordonnées » : [<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

L’exemple suivant retourne un résultat NULL en raison de l’entrée de jeton de cellule S2 non valide.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("a")
```

|point|
|---|
||
