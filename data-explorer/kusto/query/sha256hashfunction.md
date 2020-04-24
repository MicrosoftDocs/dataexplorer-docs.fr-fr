---
title: hash_sha256() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit hash_sha256() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2147f4e9f2bd3d7df8f75ac704a4e4808e69bb3c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507597"
---
# <a name="hash_sha256"></a>hash_sha256()

Retourne une valeur de hachage sha256 pour la valeur d’entrée.

**Syntaxe**

`hash_sha256(`*Source*`)`

**Arguments**

* *source*: La valeur à hachage.

**Retourne**

La valeur de hachage sha256 du scalar donné, codé comme une chaîne hex (une chaîne de caractères, dont chacun deux représentent un seul numéro Hex entre 0 et 255).

> [!WARNING]
> L’algorithme utilisé par cette fonction (SHA256) est garanti pour ne pas être modifié à l’avenir, mais est très complexe à calculer. Les utilisateurs qui ont besoin d’une fonction de hachage "léger" pour la durée d’une seule requête sont invités à utiliser le [hachage de](./hashfunction.md) la fonction () à la place.

**Exemples**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

L’exemple suivant utilise la fonction hash_sha256 pour exécuter une requête sur la colonne StartTime des données

```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```