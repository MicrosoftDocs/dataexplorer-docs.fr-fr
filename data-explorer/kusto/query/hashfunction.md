---
title: hachage() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le hachage () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f8142c42dcb0874dfbd84515e56dc8765bcba3d7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514142"
---
# <a name="hash"></a>hash()

Retourne une valeur de hachage pour la valeur d’entrée.

**Syntaxe**

`hash(`*source* `,` [ *mod*]`)`

**Arguments**

* *source*: La valeur à hachage.
* *mod*: Une valeur de module facultative à appliquer au `0` résultat de hachage, de sorte que la valeur de sortie est entre et *mod* - 1

**Retourne**

La valeur de hachage du scalaire donné, modulo la valeur mod donnée (si spécifié).

> [!WARNING]
> L’algorithme utilisé pour calculer le hachage est xxhash.
> Cet algorithme pourrait changer à l’avenir, et la seule garantie est que dans une seule requête toutes les invocations de cette méthode utilisent le même algorithme.
> Par conséquent, il est conseillé aux `hash()` utilisateurs de ne pas stocker les résultats d’un tableau. Si des valeurs persistantes de hachage sont nécessaires, envisagez [d’utiliser hash_sha256()](./sha256hashfunction.md) à la place (mais notez qu’il est beaucoup plus complexe à calculer que `hash()`).

**Exemples**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

L’exemple suivant utilise la fonction de hachage pour exécuter une requête sur 10% des données, Il est utile d’utiliser la fonction de hachage pour l’échantillonnage des données en supposant que la valeur est uniformément distribuée (Dans cet exemple StartTime valeur)

```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```