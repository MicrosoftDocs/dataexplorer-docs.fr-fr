---
title: hash ()-Azure Explorateur de données
description: Cet article décrit le hachage () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a44f817ea57a114400f45e9ca2a841150b4590a6
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226803"
---
# <a name="hash"></a>hash()

Retourne une valeur de hachage pour la valeur d’entrée.

**Syntaxe**

`hash(`*source* [ `,` *Mod*]`)`

**Arguments**

* *source*: valeur à hacher.
* *Mod*: valeur de module facultative à appliquer au résultat de hachage, afin que la valeur de sortie soit comprise entre `0` et *Mod* -1

**Retourne**

Valeur de hachage du scalaire donné, modulo la valeur de mod donnée (si spécifiée).

> [!WARNING]
> L’algorithme utilisé pour calculer le hachage est xxhash.
> Cet algorithme peut changer à l’avenir, et la seule garantie est qu’au sein d’une même requête, tous les appels de cette méthode utilisent le même algorithme.
> Par conséquent, il est recommandé aux utilisateurs de ne pas stocker les résultats de `hash()` dans une table. Si les valeurs de hachage persistantes sont requises, utilisez [hash_sha256 ()](./sha256hashfunction.md) à la place (mais notez qu’il est bien plus complexe à calculer que `hash()` ).

**Exemples**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

L’exemple suivant utilise la fonction de hachage pour exécuter une requête sur 10% des données, il est utile d’utiliser la fonction de hachage pour échantillonner les données en supposant que la valeur est distribuée uniformément (dans cet exemple, valeur StartTime)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
