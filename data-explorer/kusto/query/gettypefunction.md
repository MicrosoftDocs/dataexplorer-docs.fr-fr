---
title: gettype() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit gettype() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3a28032320948f12b2f91febc9f59c7b35ad084e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514380"
---
# <a name="gettype"></a>gettype()

Retourne le type de temps d’exécution de son seul argument.

Le type de temps d’exécution peut être différent du `dynamic`type nominal (statique) pour les expressions dont le type nominal est ; dans de `gettype()` tels cas peut être utile pour révéler le type de la valeur réelle (comment la valeur est codée dans la mémoire).

**Syntaxe**

`gettype(`*Expr*`)`

**Retourne**

Une chaîne représentant le type de runtime de son seul argument.

**Exemples**

|Expression                          |Retours      |
|------------------------------------|-------------|
|`gettype("a")`                      |`string`     |
|`gettype(111)`                      |`long`       |
|`gettype(1==1)`                     |`bool`       |
|`gettype(now())`                    |`datetime`   |
|`gettype(1s)`                       |`timespan`   |
|`gettype(parse_json('1'))`           |`int`        |
|`gettype(parse_json(' "abc" '))`     |`string`     |
|`gettype(parse_json(' {"abc":1} '))` |`dictionary` | 
|`gettype(parse_json(' [1, 2, 3] '))` |`array`      |
|`gettype(123.45)`                   |`real`       |
|`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))`|`guid`| 
|`gettype(parse_json(''))`            |`null`|