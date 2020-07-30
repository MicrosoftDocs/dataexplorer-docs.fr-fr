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
ms.openlocfilehash: 4ce05f536b8903810739c6aa7780f9eaf72c4f87
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349327"
---
# <a name="bag_keys"></a>bag_keys()

Énumère toutes les clés racines dans un objet de jeu de propriétés dynamique.

`bag_keys(`*objet dynamique*`)`

## <a name="returns"></a>Retourne

Un tableau de clés, Order n’est pas déterminé.

## <a name="examples"></a>Exemples

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
