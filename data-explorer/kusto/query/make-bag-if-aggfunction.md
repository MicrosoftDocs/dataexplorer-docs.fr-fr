---
title: make_bag_if() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit make_bag_if () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 201de637df387bb8925995a5e52c048255d1535c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512969"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if)) (fonction d’agrégation)

Renvoie `dynamic` un sac de propriété (JSON) de toutes les valeurs d’Expr dans `true`le groupe, pour lequel *Predicate* évalue à . *Expr*

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize``make_bag_if(` *Expr*, *Predicate* [`,` *MaxSize*]`)`

**Arguments**

* *Expr*: Expression `dynamic` de type qui sera utilisée pour le calcul de l’agrégation.
* *Predicate*: Predicate qui `true`doit évaluer à , afin d’Expr d’être ajouté au résultat. *Expr*
* *MaxSize* est une limite d’intégrisation facultative sur le nombre maximum d’éléments retournés (par défaut est *1048576*). La valeur MaxSize ne peut excéder 1048576.

**Retourne**

Renvoie `dynamic` un sac de propriété (JSON) de toutes les valeurs d’Expr dans le groupe qui sont des `true`sacs de propriété (dictionnaires), pour lesquels *Predicate* évalue à . *Expr*
Les valeurs non-dictionnaire seront ignorées.
Si une clé apparaît dans plus d’une ligne, une valeur arbitraire (sur les valeurs possibles pour cette clé) sera choisie.

**Voir aussi**

[`make_bag`](./make-bag-aggfunction.md)fonction, qui fait la même chose, sans expression prédicat.

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
|"prop01": "val_a", "prop03": "val_c" |

Utilisez [bag_unpack()](bag-unpackplugin.md) plugin pour transformer les clés du sac dans la sortie make_bag_if)en colonnes. 

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