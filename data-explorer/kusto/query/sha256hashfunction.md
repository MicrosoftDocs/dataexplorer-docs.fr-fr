---
title: hash_sha256 ()-Azure Explorateur de données
description: Cet article décrit hash_sha256 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32fa2f3ffefdbf1f14ed87e8e89444de322408c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372317"
---
# <a name="hash_sha256"></a>hash_sha256()

Retourne une valeur de hachage SHA256 pour la valeur d’entrée.

**Syntaxe**

`hash_sha256(`*code*`)`

**Arguments**

* *source*: valeur à hacher.

**Retourne**

Valeur de hachage SHA256 du scalaire donné, encodée sous la forme d’une chaîne hexadécimale (une chaîne de caractères, chacun deux représentant un nombre hexadécimal unique compris entre 0 et 255).

> [!WARNING]
> L’algorithme utilisé par cette fonction (SHA256) est garanti ne pas être modifié à l’avenir, mais il est très complexe à calculer. Les utilisateurs qui ont besoin d’une fonction de hachage « légère » pour la durée d’une seule requête sont invités à utiliser la fonction [hash ()](./hashfunction.md) à la place.

**Exemples**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

L’exemple suivant utilise la fonction hash_sha256 pour exécuter une requête sur la colonne StartTime des données

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
