---
title: bag_keys ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit bag_keys () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 45c4fec6e74bb8b25bfd83141fc2ee4110efa5e0
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265035"
---
# <a name="bag_keys"></a>bag_keys()

Énumère toutes les clés racines dans un objet de jeu de propriétés dynamique.

`bag_keys(`*objet dynamique*`)`

**Retourne**

Un tableau de clés, Order n’est pas déterminé.

**Exemples**

<!-- csl: https://help.kusto.windows.net/Samples -->
```
datatable(index:long, d:dynamic) [
1, dynamic({'a':'b', 'c':123}), 
2, dynamic({'a':'b', 'c':{'d':123}}),
3, dynamic({'a':'b', 'c':[{'d':123}]}),
4, dynamic(null),
5, dynamic({}),
6, dynamic('a'),
7, dynamic([])]
| extend keys = bag_keys(d)
```

|index|d|clés|
|---|---|---|
|1|{<br>  "a" : "b",<br>  "c" : 123<br>}|[<br>  « a »,<br>  "c"<br>]|
|2|{<br>  "a" : "b",<br>  "c" : {<br>    "d" : 123<br>  }<br>}|[<br>  « a »,<br>  "c"<br>]|
|3|{<br>  "a" : "b",<br>  "c" : [<br>    {<br>      "d" : 123<br>    }<br>  ]<br>}|[<br>  « a »,<br>  "c"<br>]|
|4|||
|5|{}|[]|
|6|a||
|7|[]||
