---
title: make_bag_if () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit make_bag_if () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 997ba519016bf6f6774af3f305ab78515c36c4ed
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249917"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if () (fonction d’agrégation)

Retourne un `dynamic` conteneur de propriétés (JSON) (dictionnaire) de toutes les valeurs de *'expr'* dans le groupe, pour lequel le *prédicat* a la valeur `true` .

> [!NOTE]
> Peut uniquement être utilisé dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md).

## <a name="syntax"></a>Syntaxe

`summarize``make_bag_if(` *Expr*, *prédicat* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression de type `dynamic` qui sera utilisée pour le calcul de l’agrégation.
* *Predicate*: prédicat qui doit être évalué à, afin que `true` *'expr'* soit ajouté au résultat.
* *MaxSize*: limite d’entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *1048576*). La valeur MaxSize ne peut pas dépasser 1048576.

## <a name="returns"></a>Retours

Retourne un `dynamic` conteneur de propriétés (JSON) (dictionnaire) de toutes les valeurs de *'expr'* dans le groupe qui sont des conteneurs de propriétés (dictionnaires) pour lesquels le *prédicat* a la valeur `true` .
Les valeurs autres que les dictionnaires seront ignorées.
Si une clé apparaît dans plusieurs lignes, une valeur arbitraire, en dehors des valeurs possibles pour cette clé, est sélectionnée.

> [!NOTE]
> La [`make_bag`](./make-bag-aggfunction.md) fonction est semblable à make_bag_if () sans expression de prédicat.

## <a name="examples"></a>Exemples

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag_if(p, predicate)

```

|dict|
|----|
|{"prop01" : "val_a", "prop03" : "val_c"} |

Utilisez le plug-in [bag_unpack ()](bag-unpackplugin.md) pour transformer les clés de conteneur dans la sortie make_bag_if () en colonnes. 

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag_if(p, predicate)
| evaluate bag_unpack(bag)

```

|prop01|prop03|
|---|---|
|val_a|val_c|
