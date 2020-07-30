---
title: opérateur de recherche-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur de recherche dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 5eda79977ee641d7ca7835d3d394cb943b4ebac4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347049"
---
# <a name="lookup-operator"></a>lookup, opérateur

L' `lookup` opérateur étend les colonnes d’une table de faits avec les valeurs recherchées dans une table de dimension.

```kusto
FactTable | lookup kind=leftouter (DimensionTable) on CommonColumn, $left.Col1 == $right.Col2
```

Ici, le résultat est une table qui étend `FactTable` ( `$left` ) les données de `DimensionTable` (référencées par `$right` ) en effectuant une recherche de chaque paire ( `CommonColumn` , `Col` ) à partir de la table précédente avec chaque paire ( `CommonColumn1` , `Col2` ) dans la dernière table. Pour connaître les différences entre les tables de faits et de dimension, consultez [tables de faits et de dimension](../concepts/fact-and-dimension-tables.md). 

L' `lookup` opérateur effectue une opération similaire à l' [opérateur de jointure](joinoperator.md) , avec les différences suivantes :

* Le résultat ne répète pas les colonnes de la `$right` table qui constituent la base de l’opération de jointure.
* Seuls deux types de recherche sont pris en charge, `leftouter` et `inner` , avec `leftouter` la valeur par défaut.
* En termes de performances, le système suppose par défaut que la `$left` table est la table la plus grande (des faits) et que la table `$right` est la table la plus petite (dimensions). C’est exactement l’opposé de l’hypothèse utilisée par l' `join` opérateur.
* L' `lookup` opérateur diffuse automatiquement la `$right` table à la `$left` table (essentiellement, se comporte comme si `hint.broadcast` a été spécifié). Notez que cela limite la taille de la `$right` table.

## <a name="syntax"></a>Syntaxe

*LeftTable* `|` `lookup`[ `kind` `=` ( `leftouter` | `inner` )] `(` *RightTable* `)` `on` *Attributs* RightTable

## <a name="arguments"></a>Arguments

* *LeftTable*: table ou expression tabulaire qui est la base de la recherche.
  Désigné comme `$left` .

* *RightTable*: table ou expression tabulaire utilisée pour « remplir » les nouvelles colonnes dans la table de faits. Désigné comme `$right` .

* *Attributs*: liste délimitée par des virgules d’une ou de plusieurs règles qui décrivent comment les lignes de *LeftTable* sont mises en correspondance avec les lignes de *RightTable*. Plusieurs règles sont évaluées à l’aide de l' `and` opérateur logique.
  Une règle peut être l’une des suivantes :

  |Type de règle        |Syntaxe                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |Égalité par nom |*ColumnName*                                    |`where`*LeftTable*. *ColumnName* `==` *RightTable*. *ColumnName*|
  |Égalité par valeur|`$left.`*LeftColumn* `==` `$right.` *Rightcolumn*|`where``$left.` *LeftColumn* `==` LeftColumn `$right.` *RightColumn        |

  > [!Note] 
  > En cas d’égalité par valeur, les noms de colonnes *doivent* être qualifiés par la table de propriétaire applicable dénotée par `$left` les `$right` notations et.

* `kind`: Instruction facultative sur la manière de traiter les lignes de *LeftTable* qui n’ont pas de correspondance dans *RightTable*. Par défaut, `leftouter` est utilisé, ce qui signifie que toutes ces lignes s’affichent dans la sortie avec des valeurs NULL utilisées pour les valeurs manquantes des colonnes *RightTable* ajoutées par l’opérateur. Si `inner` est utilisé, ces lignes sont omises de la sortie. (Les autres types de jointures ne sont pas pris en charge par l' `looku` opérateur p.)
  
## <a name="returns"></a>Retourne

Une table avec :

* Une colonne pour chaque colonne dans chacune des deux tables, y compris les clés correspondantes.
  Les colonnes du côté droit seront automatiquement renommées en cas de conflit de noms.
* Une ligne pour chaque correspondance entre les tables d’entrée. Une correspondance est une ligne sélectionnée dans une table, dont tous les champs `on` ont la même valeur qu’une ligne dans l’autre table. 
* Les attributs (clés de recherche) ne s’affichent qu’une seule fois dans la table de sortie.

 * `kind`non spécifié`kind=leftouter`

     Outre les correspondances internes, on trouve une ligne pour chaque ligne du côté gauche (et/ou droit), même s’il n’y a aucune correspondance. Dans ce cas, les cellules de sortie sans correspondance contiennent des valeurs null.

 * `kind=inner`

     La sortie contient une ligne pour chaque combinaison de lignes correspondantes des côtés gauche et droit.

## <a name="examples"></a>Exemples

```kusto
let FactTable=datatable(Row:string,Personal:string,Family:string) [
  "1", "Bill",   "Gates",
  "2", "Bill",   "Clinton",
  "3", "Bill",   "Clinton",
  "4", "Steve",  "Ballmer",
  "5", "Tim",    "Cook"
];
let DimTable=datatable(Personal:string,Family:string,Alias:string) [
  "Bill",  "Gates",   "billg",
  "Bill",  "Clinton", "billc",
  "Steve", "Ballmer", "steveb",
  "Tim",   "Cook",    "timc"
];
FactTable
| lookup kind=leftouter DimTable on Personal, Family
```

Ligne     | Personnel  | Famille   | Alias
--------|-----------|----------|--------
1       | Bill      | Gates    | billg
2       | Bill      | Clinton  | billc
3       | Bill      | Clinton  | billc
4       | Steve     | Avait  | steveb
5       | Tour       | Cook     | timc