---
title: make_bag_if () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit make_bag_if () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2603aab066a7f77ff36553d8898bb713ace990b7
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763801"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if () (fonction d’agrégation)

Retourne un `dynamic` conteneur de propriétés (JSON) (dictionnaire) de toutes les valeurs de *'expr'* dans le groupe, pour lequel le *prédicat* a la valeur `true` .

> [!NOTE]
> Peut uniquement être utilisé dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md).

**Syntaxe**

`summarize``make_bag_if(` *Expr*, *prédicat* [ `,` *MaxSize*]`)`

**Arguments**

* *Expr*: expression de type `dynamic` qui sera utilisée pour le calcul de l’agrégation.
* *Predicate*: prédicat qui doit être évalué à, afin que `true` *'expr'* soit ajouté au résultat.
* *MaxSize*: limite d’entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *1048576*). La valeur MaxSize ne peut pas dépasser 1048576.

**Retourne**

Retourne un `dynamic` conteneur de propriétés (JSON) (dictionnaire) de toutes les valeurs de *'expr'* dans le groupe qui sont des conteneurs de propriétés (dictionnaires) pour lesquels le *prédicat* a la valeur `true` .
Les valeurs autres que les dictionnaires seront ignorées.
Si une clé apparaît dans plusieurs lignes, une valeur arbitraire, en dehors des valeurs possibles pour cette clé, est sélectionnée.

> [!NOTE]
> La [`make_bag`](./make-bag-aggfunction.md) fonction est semblable à make_bag_if () sans expression de prédicat.

**Exemples**

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
