---
title: hash ()-Azure Explorateur de données
description: Cet article décrit le hachage () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d840ba106079a85435ee88f8b73cfc6daa7e4c5b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252313"
---
# <a name="hash"></a>hash()

Retourne une valeur de hachage pour la valeur d’entrée.

## <a name="syntax"></a>Syntaxe

`hash(`*source* [ `,` *Mod*]`)`

## <a name="arguments"></a>Arguments

* *source*: valeur à hacher.
* *Mod*: valeur de module facultative à appliquer au résultat de hachage, afin que la valeur de sortie soit comprise entre `0` et *Mod* -1

## <a name="returns"></a>Retours

Valeur de hachage du scalaire donné, modulo la valeur de mod donnée (si spécifiée).

> [!WARNING]
> L’algorithme utilisé pour calculer le hachage est xxhash.
> Cet algorithme peut changer à l’avenir, et la seule garantie est qu’au sein d’une même requête, tous les appels de cette méthode utilisent le même algorithme.
> Par conséquent, il est recommandé de ne pas stocker les résultats de `hash()` dans une table. Si les valeurs de hachage persistantes sont requises, utilisez [hash_sha256 ()](./sha256hashfunction.md) ou [hash_md5 ()](./md5hashfunction.md) à la place. Notez que ces fonctions sont plus complexes à calculer que `hash()` ).

## <a name="examples"></a>Exemples

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
