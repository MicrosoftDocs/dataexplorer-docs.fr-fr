---
title: make_bag() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit make_bag () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16f5f5663c4807a766d99c12020ff0a46c4db336
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512986"
---
# <a name="make_bag-aggregation-function"></a>make_bag)) (fonction d’agrégation)

Retourne `dynamic` un sac de propriété (JSON) de toutes les valeurs d’Expr dans le groupe. *Expr*

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize``make_bag(` *Expr* `,` [ *MaxSize*]`)`

**Arguments**

* *Expr*: Expression `dynamic` de type qui sera utilisée pour le calcul de l’agrégation.
* *MaxSize* est une limite d’intégrisation facultative sur le nombre maximum d’éléments retournés (par défaut est *1048576*). La valeur MaxSize ne peut excéder 1048576.

**Remarque**

Une variante héritée et `make_dictionary()` obsolète de cette fonction : a une limite par défaut de *MaxSize* 128.

**Retourne**

Retourne `dynamic` un sac de propriété (JSON) de toutes les valeurs d’Expr dans le groupe qui sont des sacs de propriété (dictionnaires). *Expr*
Les valeurs non-dictionnaire seront ignorées.
Si une clé apparaît dans plus d’une ligne, une valeur arbitraire (sur les valeurs possibles pour cette clé) sera choisie.

**Voir aussi**

Utilisez le [plugin bag_unpack)pour](bag-unpackplugin.md) étendre les objets JSON dynamiques en colonnes à l’aide de clés de sac de propriété. 

**Exemples**

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
|"prop01": "val_a", "prop02": "val_b", "prop03": "val_c" |

Utilisez [bag_unpack()](bag-unpackplugin.md) plugin pour transformer les clés du sac dans la sortie make_bag)en colonnes. 

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