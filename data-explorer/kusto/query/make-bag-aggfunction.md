---
title: make_bag () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit la fonction d’agrégation make_bag () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3258a847a526e0e3b6ac8f0186b0a1aaabc3ffe5
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103197"
---
# <a name="make_bag-aggregation-function"></a>make_bag () (fonction d’agrégation)

Retourne un `dynamic` conteneur de propriétés (JSON) (dictionnaire) de toutes les valeurs de *`Expr`* dans le groupe.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

`summarize``make_bag(` *`Expr`* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression de type `dynamic` utilisée pour les calculs d’agrégation.
* *MaxSize* est une limite d’entier facultative sur le nombre maximal d’éléments retournés. La valeur par défaut est *1048576*. La valeur MaxSize ne peut pas dépasser *1048576*.

**Remarque**

Une variante héritée et obsolète de la fonction `make_dictionary()` a une limite par défaut de *MaxSize* = 128.

## <a name="returns"></a>retourne :

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
