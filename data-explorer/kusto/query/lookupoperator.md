---
title: opérateur de recherche - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de recherche dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 1325a1b839b62baacade4cf7c64cd278aeeabaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513054"
---
# <a name="lookup-operator"></a>lookup, opérateur

L’opérateur `lookup` étend les colonnes d’un tableau de faits avec des valeurs recherchées dans une table de dimension.

```kusto
FactTable | lookup kind=leftouter (DimensionTable) on CommonColumn, $left.Col1 == $right.Col2
```

Ici, le résultat est un `FactTable` `$left`tableau qui `DimensionTable` étend le `$right`( ) avec des données`CommonColumn`de`Col`(référencé par )`CommonColumn1``Col2`en effectuant un lookup de chaque paire ( , ) à partir de la table d’entrée avec chaque paire ( , ) dans le dernier tableau. Pour les différences entre les tables de fait et de dimension, voir [les tables de fait et de dimension](../concepts/fact-and-dimension-tables.md). 

L’opérateur `lookup` effectue une opération similaire à celle de [l’opérateur d’adhénage](joinoperator.md) avec les différences suivantes :

* Le résultat ne répète `$right` pas les colonnes de la table qui sont la base de l’opération de jointure.
* Seuls deux types de recherche `leftouter` `inner`sont `leftouter` pris en charge, et , avec être la valeur par défaut.
* En termes de performances, le système `$left` suppose par défaut que la table `$right` est le tableau plus grand (faits), et le tableau est le plus petit (dimensions) table. C’est exactement à l’opposé de l’hypothèse utilisée par l’opérateur. `join`
* L’opérateur `lookup` diffuse `$right` automatiquement la `$left` table à la table `hint.broadcast` (essentiellement, se comporte comme si elle était spécifiée). Notez que cela limite `$right` la taille de la table.

**Syntaxe**

*LeftTable* `|` `lookup` `kind` `=` [`leftouter`|`inner`( `(` )] `)` `on` *Attributs* *RightTable*

**Arguments**

* *LeftTable*: La table ou l’expression tabulaire qui est la base de la recherche.
  Dénoté `$left`comme .

* *RightTable*: La table ou l’expression tabulaire qui est utilisée pour «peupler» de nouvelles colonnes dans le tableau des faits. Dénoté `$right`comme .

* *Attributs*: Une liste délimitée par virgule d’une ou plusieurs règles qui décrivent comment les lignes de *LeftTable* sont appariées aux rangées de *RightTable*. Plusieurs règles sont évaluées à l’aide de l’opérateur `and` logique.
  Une règle peut être l’une des règles :

  |Type de règle        |Syntaxe                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |Égalité par nom |*ColumnName*                                    |`where`*LeftTable*. *ColumnName* `==` *RightTable*. *Nom de colonne*|
  |Égalité par valeur|`$left.`*LeftColumn* `==` `$right.` *RightColumn*|`where``$left.` *LeftColumn* `==` `$right.`-RightColumn        |

  > [!Note] 
  > En cas d'«égalité par valeur», les noms de *colonnes* doivent `$left` `$right` être qualifiés avec le tableau du propriétaire applicable dénoté par et notations.

* `kind`: Une instruction facultative sur la façon de traiter les lignes dans *LeftTable* qui n’ont pas de correspondance dans *RightTable*. Par défaut, `leftouter` est utilisé, ce qui signifie que toutes ces lignes apparaîtront dans la sortie avec des valeurs nulles utilisées pour les valeurs manquantes des colonnes *RightTable* ajoutées par l’opérateur. Si `inner` elle est utilisée, ces lignes sont omises de la sortie. (D’autres types d’adhésion `looku`ne sont pas pris en charge par l’opérateur p.)
  
**Retourne**

Une table avec :

* Une colonne pour chaque colonne dans chacune des deux tables, y compris les clés correspondantes.
  Les colonnes du côté droit seront automatiquement rebaptisées en cas de conflit de nom.
* Une ligne pour chaque correspondance entre les tables d’entrée. Une correspondance est une ligne sélectionnée dans une table, dont tous les champs `on` ont la même valeur qu’une ligne dans l’autre table. 
* Les Attributs (clés de recherche) n’apparaîtront qu’une seule fois dans le tableau de sortie.

 * `kind`Quelconque`kind=leftouter`

     Outre les correspondances internes, on trouve une ligne pour chaque ligne du côté gauche (et/ou droit), même s’il n’y a aucune correspondance. Dans ce cas, les cellules de sortie sans correspondance contiennent des valeurs null.

 * `kind=inner`

     La sortie contient une ligne pour chaque combinaison de lignes correspondantes des côtés gauche et droit.

**Exemples**

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

Ligne     | Personal  | Famille   | Alias
--------|-----------|----------|--------
1       | Bill      | Portes    | billg
2       | Bill      | Clinton  | billc (en)
3       | Bill      | Clinton  | billc (en)
4       | Steve     | Ballmer  | Steveb
5       | Tim       | Cuisiner     | timc timc