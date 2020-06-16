---
title: plug-in schema_merge-Azure Explorateur de données
description: Cet article décrit schema_merge plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: b1f3ef10ac5cee3eb9bc1c1dca4c0de26bd85477
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780199"
---
# <a name="schema_merge-plugin"></a>plug-in schema_merge

Fusionne les définitions de schéma tabulaire dans le schéma unifié. 

Les définitions de schéma sont supposées être au format produit par l' [`getschema`](./getschemaoperator.md) opérateur.

L' `schema merge` opération joint des colonnes dans les schémas d’entrée et tente de réduire les types de données aux types courants. Si les types de données ne peuvent pas être réduits, une erreur s’affiche dans la colonne problématique.

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

**Syntaxe**

`T``|` `evaluate` `schema_merge(` *PreserveOrder*`)`

**Arguments**

* *PreserveOrder*: (facultatif) lorsque la valeur est `true` , indique au plug-in de valider l’ordre des colonnes comme défini par le premier schéma tabulaire conservé. Si la même colonne se trouve dans plusieurs schémas, le numéro de colonne doit être identique à l’ordinal de colonne du premier schéma dans lequel elle apparaît. La valeur par défaut est `true`.

**Retourne**

Le `schema_merge` plug-in retourne une sortie semblable à celle qui est [`getschema`](./getschemaoperator.md) retournée par l’opérateur.

**Exemples**

Fusionnez avec un schéma auquel une nouvelle colonne est ajoutée.

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, HttpStatus:int, Referrer:string)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*Résultat*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|string|
|httpStatus|1|System.Int32|int|
|Referrer|2|System.String|string|

Fusionnez avec un schéma dont l’ordre des colonnes est différent ( `HttpStatus` modifications ordinales de `1` à `2` dans la nouvelle variante).

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*Résultat*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|string|
|Referrer|1|System.String|string|
|httpStatus|-1|ERREUR (type de CSL inconnu : erreur (colonnes non ordonnées))|ERREUR (les colonnes sont dans le désordre)|

Fusionnez avec un schéma dont le classement des colonnes est différent, mais avec `PreserveOrder` défini sur `false` .

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge(PreserveOrder = false)
```

*Résultat*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|Uri|0|System.String|string
|Referrer|1|System.String|string
|httpStatus|2|System.Int32|int|
