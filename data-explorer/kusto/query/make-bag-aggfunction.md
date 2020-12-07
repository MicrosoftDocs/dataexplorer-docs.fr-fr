---
title: make_bag () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit la fonction d’agrégation make_bag () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 936b8aedb4244836eff8c8618a53693395a0343c
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303255"
---
# <a name="make_bag-aggregation-function"></a>make_bag () (fonction d’agrégation)

Retourne un `dynamic` conteneur de propriétés (JSON) (dictionnaire) de toutes les valeurs de *`Expr`* dans le groupe.

* Peut être utilisé uniquement dans un contexte d’agrégation à l’intérieur de l’opérateur [summarize](summarizeoperator.md).

## <a name="syntax"></a>Syntaxe

`summarize``make_bag(` *`Expr`* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression de type `dynamic` utilisée pour les calculs d’agrégation.
* *MaxSize* est une limite d’entier facultative sur le nombre maximal d’éléments retournés. La valeur par défaut est *1048576*. La valeur MaxSize ne peut pas dépasser *1048576*.

> [!NOTE]
> `make_dictionary()` est une version héritée et obsolète de `make_bag()` . La version héritée a une limite par défaut de *MaxSize* = 128.

## <a name="returns"></a>Retours

Retourne un `dynamic` conteneur de propriétés (JSON) (dictionnaire) de toutes les valeurs de *`Expr`* dans le groupe, qui sont des conteneurs de propriétés.
Les valeurs autres que les dictionnaires seront ignorées.
Si une clé apparaît dans plusieurs lignes, une valeur arbitraire, en dehors des valeurs possibles pour cette clé, est sélectionnée.

## <a name="see-also"></a>Voir aussi

Utilisez le plug-in [bag_unpack ()](bag-unpackplugin.md) pour développer des objets JSON dynamiques dans des colonnes qui utilisent des clés de conteneur de propriétés. 

## <a name="examples"></a>Exemples

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag(p)

```

|dict|
|----|
|{"prop01" : "val_a", "prop02" : "val_b", "prop03" : "val_c"} |

Utilisez le plug-in [bag_unpack ()](bag-unpackplugin.md) pour transformer les clés de conteneur dans la sortie make_bag () en colonnes. 

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag(p)
| evaluate bag_unpack(bag) 

```

|prop01|prop02|prop03|
|---|---|---|
|val_a|val_b|val_c|
