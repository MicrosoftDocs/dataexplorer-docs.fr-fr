---
title: MV-opérateur apply-Azure Explorateur de données
description: Cet article décrit l’opérateur MV-Apply dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c15b3aaf14c9f859c3d93c48406ec642897e59d4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243213"
---
# <a name="mv-apply-operator"></a>mv-apply, opérateur

Applique une sous-requête à chaque enregistrement et retourne l’Union des résultats de toutes les sous-requêtes.

Par exemple, supposons qu’une table `T` possède une colonne `Metric` de type `dynamic` dont les valeurs sont des tableaux de `real` nombres. La requête suivante localise les deux valeurs les plus importantes dans chaque `Metric` valeur et retourne les enregistrements correspondant à ces valeurs.

```kusto
T | mv-apply Metric to typeof(real) on 
(
   top 2 by Metric desc
)
```

L' `mv-apply` opérateur a les étapes de traitement suivantes :

1. Utilise l' [`mv-expand`](./mvexpandoperator.md) opérateur pour développer chaque enregistrement de l’entrée en sous-tables.
1. Applique la sous-requête pour chacune des sous-tables.
1. Ajoute zéro colonne ou plus à la sous-table résultante. Ces colonnes contiennent les valeurs des colonnes sources qui ne sont pas développées et sont répétées là où cela est nécessaire.
1. Retourne l’Union des résultats.

L' `mv-apply` opérateur obtient les entrées suivantes :

1. Une ou plusieurs expressions qui s’évaluent en tableaux dynamiques à développer.
   Le nombre d’enregistrements dans chaque sous-table développée est la longueur maximale de chacun de ces tableaux dynamiques. Des valeurs NULL sont ajoutées lorsque plusieurs expressions sont spécifiées et que les tableaux correspondants ont des longueurs différentes.

1. Éventuellement, les noms pour assigner les valeurs des expressions après l’expansion.
   Ces noms deviennent les noms de colonnes dans les sous-tables.
   S’il n’est pas spécifié, le nom d’origine de la colonne est utilisé lorsque l’expression est une référence de colonne. Un nom aléatoire est utilisé dans le cas contraire. 

   > [!NOTE]
   > Il est recommandé d’utiliser les noms de colonnes par défaut.

1. Types de données des éléments de ces tableaux dynamiques, après expansion.
   Celles-ci deviennent les types de colonne des colonnes dans les sous-tables.
   En l'absence de spécification, `dynamic` est utilisé.

1. Le cas échéant, nom d’une colonne à ajouter aux sous-tables qui spécifient l’index de base 0 de l’élément dans le tableau qui a généré l’enregistrement de la sous-table.

1. Éventuellement, le nombre maximal d’éléments de tableau à développer.

L' `mv-apply` opérateur peut être considéré comme une généralisation de l' [`mv-expand`](./mvexpandoperator.md) opérateur (en fait, ce dernier peut être implémenté par le premier, si la sous-requête comprend uniquement des projections).

## <a name="syntax"></a>Syntaxe

*T* `|` `mv-apply` [*itemIndex*] *ColumnsToExpand* [*RowLimit*] `on` `(` *sous-requête*`)`

Où *itemIndex* a la syntaxe suivante :

`with_itemindex``=` *IndexColumnName*

*ColumnsToExpand* est une liste séparée par des virgules d’un ou plusieurs éléments de la forme :

[*Name* `=` ] *ArrayExpression* [ `to` `typeof` `(` *TypeName* `)` ]

*RowLimit* est simplement :

`limit`*RowLimit*

et *sous-requête* a la même syntaxe qu’une instruction de requête.

## <a name="arguments"></a>Arguments

* *ItemIndex*: si utilisé, indique le nom d’une colonne de type `long` qui est ajoutée à l’entrée dans le cadre de la phase d’extension de tableau et indique l’index de tableau de base 0 de la valeur développée.

* *Nom*: s’il est utilisé, nom pour assigner les valeurs développées par le tableau de chaque expression développée par un tableau.
  S’il n’est pas spécifié, le nom de la colonne sera utilisé s’il est disponible.
  Un nom aléatoire est généré si *ArrayExpression* n’est pas un nom de colonne simple.

* *ArrayExpression*: expression de type `dynamic` dont les valeurs seront développées par tableau.
  Si l’expression est le nom d’une colonne dans l’entrée, la colonne d’entrée est supprimée de l’entrée et une nouvelle colonne du même nom (ou *ColumnName* si spécifié) apparaît dans la sortie.

* *TypeName*: si utilisé, nom du type que les éléments individuels du `dynamic` tableau *ArrayExpression* prennent. Les éléments qui ne sont pas conformes à ce type seront remplacés par une valeur null.
  (Si non spécifié, `dynamic` est utilisé par défaut.)

* *RowLimit*: en cas d’utilisation, limite du nombre d’enregistrements à générer à partir de chaque enregistrement de l’entrée.
  (Si non spécifié, 2147483647 est utilisé.)

* *Sous-requête*: expression de requête tabulaire avec une source tabulaire implicite qui est appliquée à chaque sous-table développée par un tableau.

**Notes**

* Contrairement à l' [`mv-expand`](./mvexpandoperator.md) opérateur, l' `mv-apply` opérateur prend en charge l’expansion de tableau uniquement. Le développement des conteneurs de propriétés n’est pas pris en charge.

## <a name="examples"></a>Exemples

## <a name="getting-the-largest-element-from-the-array"></a>Obtention de l’élément le plus grand à partir du tableau

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 8 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply element=l to typeof(long) on 
(
   top 1 by element
)
```

|`xMod2`|l           |element|
|-----|------------|-------|
|1    |[1, 3, 5, 7]|7      |
|0    |[2, 4, 6, 8]|8      |

## <a name="calculating-the-sum-of-the-largest-two-elements-in-an-array"></a>Calcul de la somme des deux éléments les plus importants dans un tableau

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 8 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply l to typeof(long) on
(
   top 2 by l
   | summarize SumOfTop2=sum(l)
)
```

|`xMod2`|l        |SumOfTop2|
|-----|---------|---------|
|1    |[1, 3, 5, 7]|12       |
|0    |[2, 4, 6, 8]|14       |

## <a name="using-with_itemindex-for-working-with-a-subset-of-the-array"></a>Utilisation `with_itemindex` de pour l’utilisation d’un sous-ensemble du tableau

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _data =
range x from 1 to 10 step 1
| summarize l=make_list(x) by xMod2 = x % 2;
_data
| mv-apply with_itemindex=index element=l to typeof(long) on 
(
   // here you have 'index' column
   where index >= 3
)
| project index, element
```

|index|element|
|---|---|
|3|7|
|4|9|
|3|8|
|4|10|

## <a name="see-also"></a>Voir aussi

* opérateur [MV-Expand](./mvexpandoperator.md) .
