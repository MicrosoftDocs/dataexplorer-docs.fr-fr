---
title: row_cumsum ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit row_cumsum () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ad1df20972238bee17217f5d9de19a020b4cbce
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242850"
---
# <a name="row_cumsum"></a>row_cumsum()

Calcule la somme cumulée d’une colonne dans un [ensemble de lignes sérialisé](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Syntaxe

`row_cumsum``(` *Terme* [ `,` *redémarrer*]`)`

* *Terme* est une expression indiquant la valeur à additionner.
  L’expression doit être une scalaire de l’un des types suivants : `decimal` , `int` , `long` ou `real` . Les valeurs de *terme* NULL n’affectent pas la somme.
* *Restart* est un argument facultatif de type `bool` qui indique à quel moment l’opération d’accumulation doit être redémarrée (rétablit la valeur 0). Il peut être utilisé pour indiquer des partitions de données ; consultez le deuxième exemple ci-dessous.

## <a name="returns"></a>Retours

La fonction retourne la somme cumulée de son argument.

## <a name="examples"></a>Exemples

L’exemple suivant montre comment calculer la somme cumulée des nombres entiers pairs.

```kusto
datatable (a:long) [
    1, 2, 3, 4, 5, 6, 7, 8, 9, 10
]
| where a%2==0
| serialize cs=row_cumsum(a)
```

a    | cs
-----|-----
2    | 2
4    | 6
6    | 12
8    | 20
10   | 30

Cet exemple montre comment calculer la somme cumulée (ici, de `salary` ) lorsque les données sont partitionnées (ici, par `name` ) :

```kusto
datatable (name:string, month:int, salary:long)
[
    "Alice", 1, 1000,
    "Bob",   1, 1000,
    "Alice", 2, 2000,
    "Bob",   2, 1950,
    "Alice", 3, 1400,
    "Bob",   3, 1450,
]
| order by name asc, month asc
| extend total=row_cumsum(salary, name != prev(name))
```

name   | month  | traitements  | total
-------|--------|---------|------
Alice  | 1      | 1 000    | 1 000
Alice  | 2      | 2000    | 3000
Alice  | 3      | 1400    | 4400
Bob    | 1      | 1 000    | 1 000
Bob    | 2      | 1950    | 2950
Bob    | 3      | 1450    | 4400