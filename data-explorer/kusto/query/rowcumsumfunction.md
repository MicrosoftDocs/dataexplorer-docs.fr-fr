---
title: row_cumsum() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit row_cumsum() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 92ebec75dcd7e44d59f964dc735e857f22f1ad00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510215"
---
# <a name="row_cumsum"></a>row_cumsum()

Calcule la somme cumulative d’une colonne dans un [ensemble de rangées sérialisées](./windowsfunctions.md#serialized-row-set).

**Syntaxe**

`row_cumsum``(` *Terme* `,` [ *Redémarrer*]`)`

* *Le terme* est une expression indiquant la valeur à résumer.
  L’expression doit être un scalaire de `decimal` `int`l’un des types suivants: , , `long`, ou `real`. Les valeurs *à durée* nulle n’affectent pas la somme.
* *Le redémarrage* est un `bool` argument facultatif de type qui indique quand l’opération d’accumulation doit être redémarrée (recul à 0). Il peut être utilisé pour indiquer les partitions des données; voir le deuxième exemple ci-dessous.

**Retourne**

La fonction renvoie la somme cumulative de son argument.

**Exemples**

L’exemple suivant montre comment calculer la somme cumulative des premiers même integers.

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

Cet exemple montre comment calculer la somme `salary`cumulative (ici, de ) `name`lorsque les données sont partitionnées (ici, par ):

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

name   | month  | Salaire  | total
-------|--------|---------|------
Alice  | 1      | 1 000    | 1 000
Alice  | 2      | 2000    | 3000
Alice  | 3      | 1400    | 4400
Bob    | 1      | 1 000    | 1 000
Bob    | 2      | 1950    | 2950
Bob    | 3      | 1450    | 4400