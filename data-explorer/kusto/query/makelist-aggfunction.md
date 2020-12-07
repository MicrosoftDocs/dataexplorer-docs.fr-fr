---
title: make_list () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit make_list () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: bd923724a391cfc1638057fec654a1bd74b78479
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303371"
---
# <a name="make_list-aggregation-function"></a>make_list () (fonction d’agrégation)

Retourne un tableau (JSON) `dynamic` de toutes les valeurs de *Expr* dans le groupe.

* Peut être utilisé uniquement dans un contexte d’agrégation à l’intérieur de l’opérateur [summarize](summarizeoperator.md).

## <a name="syntax"></a>Syntaxe

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation.
* *MaxSize* est une limite d’entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *1048576*). La valeur MaxSize ne peut pas dépasser 1048576.

> [!NOTE]
> `makelist()` est une version héritée et obsolète de la `make_list` fonction. La version héritée a une limite par défaut de *MaxSize* = 128.

## <a name="returns"></a>Retours

Retourne un tableau (JSON) `dynamic` de toutes les valeurs de *Expr* dans le groupe.
Si l’entrée de l' `summarize` opérateur n’est pas triée, l’ordre des éléments dans le tableau résultant n’est pas défini.
Si l’entrée de l' `summarize` opérateur est triée, l’ordre des éléments dans le tableau résultant suit celui de l’entrée.

> [!TIP]
> Utilisez la [`array_sort_asc()`](./arraysortascfunction.md) [`array_sort_desc()`](./arraysortdescfunction.md) fonction ou pour créer une liste ordonnée à l’aide d’une clé.

## <a name="examples"></a>Exemples

### <a name="one-column"></a>Une colonne

L’exemple le plus simple consiste à faire une liste à partir d’une seule colonne :

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name)
```

|liste|
|---|
|[« triangle », « Square », « Rectangle », « Pentagone », « hexagone », « heptagon », « Octogon », « non-agoniste », « Decagon »]|

### <a name="using-the-by-clause"></a>Utilisation de la clause’by'

Dans la requête suivante, vous regroupez à l’aide de la `by` clause :

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name) by isEvenSideCount = sideCount % 2 == 0
```

|liste|isEvenSideCount|
|---|---|
|false|["triangle", "Pentagone", "heptagon", "not Agonist"]|
|true|[« Square », « Rectangle », « hexagone », « Octogon », « Decagon »]|

### <a name="packing-a-dynamic-object"></a>Compression d’un objet dynamique

Vous pouvez [compresser](./packfunction.md) un objet dynamique dans une colonne avant d’en faire une liste, comme illustré dans la requête suivante :

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| extend d = pack("name", name, "sideCount", sideCount)
| summarize mylist = make_list(d) by isEvenSideCount = sideCount % 2 == 0
```

|liste|isEvenSideCount|
|---|---|
|false|[{"Name" : "triangle", "sideCount" : 3}, {"Name" : "Pentagone", "sideCount" : 5}, {"Name" : "heptagon", "sideCount" : 7}, {"Name" : "unagonist", "sideCount" : 9}]|
|true|[{"Name" : "Square", "sideCount" : 4}, {"Name" : "rectangle", "sideCount" : 4}, {"Name" : "hexagone", "sideCount" : 6}, {"Name" : "Octogon", "sideCount" : 8}, {"Name" : "Decagon", "sideCount" : 10}]|

## <a name="see-also"></a>Voir aussi

[`make_list_if`](./makelistif-aggfunction.md) est semblable à `make_list` , sauf qu’il accepte également un prédicat.
