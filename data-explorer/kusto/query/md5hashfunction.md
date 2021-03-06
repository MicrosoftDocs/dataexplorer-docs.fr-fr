---
title: hash_md5 ()-Azure Explorateur de données
description: Cet article décrit hash_md5 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/29/2020
ms.openlocfilehash: 17b3e0d13f8048ccac90add4eee6f02f3d2e2959
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346794"
---
# <a name="hash_md5"></a>hash_md5()

Retourne une valeur de hachage MD5 pour la valeur d’entrée.

## <a name="syntax"></a>Syntaxe

`hash_md5(`*code*`)`

## <a name="arguments"></a>Arguments

* *source*: valeur à hacher.

## <a name="returns"></a>Retourne

Valeur de hachage MD5 du scalaire donné, encodée sous la forme d’une chaîne hexadécimale (une chaîne de caractères, chacun deux représentant un nombre hexadécimal unique compris entre 0 et 255).

> [!WARNING]
> L’algorithme utilisé par cette fonction (MD5) est garanti ne pas être modifié à l’avenir, mais il est très complexe à calculer. Les utilisateurs qui ont besoin d’une fonction de hachage « légère » pour la durée d’une seule requête sont invités à utiliser la fonction [hash ()](./hashfunction.md) à la place.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_md5("World"),
h2=hash_md5(datetime(2020-01-01))
```

|S1|H2|
|---|---|
|f5a7924e621e84c9280a9a27e1bcb7f6|786c530672d1f8db31fee25ea8a9390b|


L’exemple suivant utilise la `hash_md5()` fonction pour agréger StormEvents en fonction de la valeur de hachage MD5 de l’État. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize StormCount = count() by State, StateHash=hash_md5(State)
| top 5 by StormCount
```

|State|StateHash|StormCount|
|---|---|---|
|TEXAS|3b00dbe6e07e7485a1c12d36c8e9910a|4701|
|KANSAS|e1338d0ac8be43846cf9ae967bd02e7f|3166|
|IOWA|6d4a7c02942f093576149db764d4e2d2|2337|
|ILLINOIS|8c00d9e0b3fcd55aed5657e42cc40cf1|2022|
|MISSOURI|2d82f0c963c0763012b2539d469e5008|2016|
