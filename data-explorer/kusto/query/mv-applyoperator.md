---
title: opérateur mv-application - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur mv-application dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f24bf7721707aa1ba3ae9f0aad49b247f08c2498
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512306"
---
# <a name="mv-apply-operator"></a>mv-apply, opérateur

L’opérateur mv-application étend chaque enregistrement dans son tableau d’entrée dans un sous-tableau, applique une sous-requête à chaque sous-table et renvoie l’union des résultats de toutes les sous-requêtes.

Supposons, par `T` exemple, `Metric` qu’une table comporte une colonne de type `dynamic` dont les valeurs sont des tableaux de `real` nombres. La requête suivante localisera les deux `Metric` plus grandes valeurs de chaque valeur et retournera les enregistrements correspondant à ces valeurs.

```kusto
T | mv-apply Metric to typeof(real) on (top 2 by Metric desc)
```

En général, l’opérateur mv-application peut être considéré comme ayant les étapes de traitement suivantes:

1. Il utilise l’opérateur [d’expansion mv](./mvexpandoperator.md) pour étendre chaque enregistrement dans l’entrée dans les sous-tables.
2. Il applique la sous-requête pour chacune des sous-tables.
3. Il prépend zéro ou plus de colonnes à chaque sous-table résultante, contenant les valeurs (répétées si nécessaire) des colonnes sources qui ne sont pas élargies.
4. Il renvoie l’union des résultats.

L’opérateur mv-expand obtient les intrants suivants :

1. Une ou plusieurs expressions qui s’évaluent en tableaux dynamiques pour se développer.
   Le nombre d’enregistrements dans chaque sous-tableau élargi est la longueur maximale de chacun de ces tableaux dynamiques. (Si plusieurs expressions sont spécifiées, mais les tableaux correspondants sont de longueurs différentes, des valeurs nulles sont introduites si nécessaire.)

2. Optionnellement, les noms pour attribuer les valeurs des expressions, après l’expansion.
   Ceux-ci deviennent les noms des colonnes dans les sous-tables.
   S’il n’est pas précisé, le nom original de la colonne est utilisé (si l’expression est une référence de colonne), ou un nom aléatoire est utilisé (autrement).

   > [!NOTE]
   > Il est recommandé d’utiliser les noms de colonne par défaut.

3. Les types de données des éléments de ces tableaux dynamiques, après l’expansion.
   Ceux-ci deviennent les types de colonnes des colonnes dans les sous-tables.
   En l'absence de spécification, `dynamic` est utilisé.

4. Optionnellement, le nom d’une colonne à ajouter aux sous-tables qui spécifie l’index à 0 de l’élément dans le tableau qui a abouti à l’enregistrement sous-table.

5. Optionnellement, le nombre maximum d’éléments de tableau à agrandir.

L’opérateur mv-application peut être considéré comme une généralisation de [l’opérateur d’expansion mv](./mvexpandoperator.md) (en fait, le second peut être mis en œuvre par le premier, si la sous-requête ne comprend que des projections.)

**Syntaxe**

*T* `|` T `mv-apply` [*ItemIndex*] *ColumnsToExpand* `on` `(` [*RowLimit*] *SubQuery*`)`

Où *ItemIndex* a la syntaxe:

`with_itemindex``=` *IndexColumnName*

*ColumnsToExpand* est une liste séparée par virgule d’un ou plusieurs éléments du formulaire :

[*Nom* `=`] *ArrayExpression* `to` `typeof` `(`[ *Typename*`)`]

*RowLimit* est simplement:

`limit`*RowLimit RowLimit RowLimit RowLim*

et *SubQuery* a la même syntaxe de toute déclaration de requête.

**Arguments**

* *ItemIndex*: S’il est utilisé, `long` indique le nom d’une colonne de type qui est annexée à l’entrée dans le cadre de la phase d’expansion du tableau et indique l’indice de tableau à 0 de la valeur élargie.

* *Nom*: S’il est utilisé, le nom pour attribuer les valeurs étendues de tableau de chaque expression élargie de tableau.
  (Si elle n’est pas précisée, le nom de la colonne sera utilisé s’il est disponible, ou un nom aléatoire généré si *ArrayExpression* n’est pas un nom de colonne simple.)

* *ArrayExpression*: Une `dynamic` expression de type dont les valeurs seront élargies.
  Si l’expression est le nom d’une colonne dans l’entrée, la colonne d’entrée est supprimée de l’entrée et une nouvelle colonne du même nom (ou *ColumnName* si spécifié) apparaîtra dans la sortie.

* *Typename : S’il*est utilisé, le nom `dynamic` du type que prennent les éléments individuels du tableau *ArrayExpression.* Les éléments qui ne sont pas conformes à ce type seront remplacés par une valeur nulle.
  (Si elle n’est pas spécifiée, `dynamic` est utilisée par défaut.)

* *RowLimit*: Si vous êtes utilisé, une limite au nombre d’enregistrements à générer à partir de chaque enregistrement de l’entrée.
  (Si elle n’est pas spécifiée, 2147483647 est utilisée.)

* *Sous-Pays*: Une expression de requête tabulaire avec une source tabulaire implicite qui est appliquée à chaque sous-table étendue à la table.

**Remarques**

* Contrairement à [l’opérateur d’expansion mv,](./mvexpandoperator.md) l’opérateur mv-appliquer ne prend en charge l’expansion du réseau que. Il n’y a pas de soutien pour l’expansion des sacs de propriété.

**Exemples**

## <a name="getting-the-largest-element-from-the-array"></a>Obtenir le plus grand élément de la gamme

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

|xMod2|l           |element|
|-----|------------|-------|
|1    |[1, 3, 5, 7]|7      |
|0    |[2, 4, 6, 8]|8      |

## <a name="calculating-sum-of-largest-two-elments-in-an-array"></a>Calcul de la somme des deux plus grands éléments dans un tableau

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

|xMod2|l        |SumOfTop2 (en)|
|-----|---------|---------|
|1    |[1,3,5,7]|12       |
|0    |[2,4,6,8]|14       |


## <a name="using-with_itemindex-for-working-with-subset-of-the-array"></a>Utilisation `with_itemindex` pour travailler avec le sous-ensemble du tableau

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

## <a name="using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key"></a>Utilisation `mv-apply` de l’opérateur `makelist` pour trier la production d’agrégats par

```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',        datetime(2019-07-15),   "user1",
    'ls',           datetime(2019-07-02),   "user1",
    'dir',          datetime(2019-07-22),   "user1",
    'mkdir',        datetime(2019-07-14),   "user1",
    'rm',           datetime(2019-07-27),   "user1",
    'pwd',          datetime(2019-07-25),   "user1",
    'rm',           datetime(2019-07-23),   "user2",
    'pwd',          datetime(2019-07-25),   "user2",
]
| summarize commands_details = make_list(pack('command', command, 'command_time', command_time)) by user_id
| mv-apply command_details = commands_details on
(
    order by todatetime(command_details['command_time']) asc
    | summarize make_list(tostring(command_details['command']))
)
| project-away commands_details 
```

|user_id|list_command_details_command|
|---|---|
|user1|[<br>  "ls",<br>  "mkdir",<br>  "chmod",<br>  "dir",<br>  "pwd",<br>  "rm"<br>]|
|user2|[<br>  "rm",<br>  "pwd"<br>]|


**Voir aussi**

* [opérateur d’expansion mv.](./mvexpandoperator.md)