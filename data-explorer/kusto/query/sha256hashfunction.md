---
title: hash_sha256 ()-Azure Explorateur de données
description: Cet article décrit hash_sha256 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3d6cd8b71ac5abed2d56e567c992a15512141063
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242605"
---
# <a name="hash_sha256"></a>hash_sha256()

Retourne une valeur de hachage SHA256 pour la valeur d’entrée.

## <a name="syntax"></a>Syntaxe

`hash_sha256(`*code*`)`

## <a name="arguments"></a>Arguments

* *source*: valeur à hacher.

## <a name="returns"></a>Retours

Valeur de hachage SHA256 du scalaire donné, encodée sous la forme d’une chaîne hexadécimale (une chaîne de caractères, chacun deux représentant un nombre hexadécimal unique compris entre 0 et 255).

> [!WARNING]
> L’algorithme utilisé par cette fonction (SHA256) est garanti ne pas être modifié à l’avenir, mais il est très complexe à calculer. Les utilisateurs qui ont besoin d’une fonction de hachage « légère » pour la durée d’une seule requête sont invités à utiliser la fonction [hash ()](./hashfunction.md) à la place.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_sha256("World"),
h2=hash_sha256(datetime(2020-01-01))
```

|S1|H2|
|---|---|
|78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524|ba666752dc1a20eb750b0eb64e780cc4c968bc9fb8813461c1d7e750f302d71d|

L’exemple suivant utilise la `hash_sha256()` fonction pour agréger StormEvents en fonction de la valeur de hachage SHA256 de l’État. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count() by State, StateHash=hash_sha256(State)
| top 5 by StormCount desc
```

|State|StateHash|StormCount|
|---|---|---|
|TEXAS|9087f20f23f91b5a77e8406846117049029e6798ebbd0d38aea68da73a00ca37|4701|
|KANSAS|c80e328393541a3181b258cdb4da4d00587c5045e8cf3bb6c8fdb7016b69cc2e|3166|
|IOWA|f85893dca466f779410f65cd904fdc4622de49e119ad4e7c7e4a291ceed1820b|2337|
|ILLINOIS|ae3eeabfd7eba3d9a4ccbfed6a9b8cff269dc43255906476282e0184cf81b7fd|2022|
|MISSOURI|d15dfc28abc3ee73b7d1f664a35980167ca96f6f90e034db2a6525c0b8ba61b1|2016|
