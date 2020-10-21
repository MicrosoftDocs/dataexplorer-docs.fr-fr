---
title: plug-in schema_merge-Azure Explorateur de données
description: Cet article décrit schema_merge plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 9a6f25a7ea1c75211d043fdbca7f99c16ff98e1b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250333"
---
# <a name="schema_merge-plugin"></a>schema_merge, plug-in

Fusionne les définitions de schéma tabulaire dans le schéma unifié. 

Les définitions de schéma sont supposées être au format produit par l' [`getschema`](./getschemaoperator.md) opérateur.

L' `schema merge` opération joint des colonnes dans les schémas d’entrée et tente de réduire les types de données aux types courants. Si les types de données ne peuvent pas être réduits, une erreur s’affiche dans la colonne problématique.

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

## <a name="syntax"></a>Syntaxe

`T``|` `evaluate` `schema_merge(` *PreserveOrder*`)`

## <a name="arguments"></a>Arguments

* *PreserveOrder*: (facultatif) lorsque la valeur est `true` , indique au plug-in de valider l’ordre des colonnes comme défini par le premier schéma tabulaire conservé. Si la même colonne se trouve dans plusieurs schémas, le numéro de colonne doit être identique à l’ordinal de colonne du premier schéma dans lequel elle apparaît. La valeur par défaut est `true`.

## <a name="returns"></a>Retours

Le `schema_merge` plug-in retourne une sortie semblable à celle qui est [`getschema`](./getschemaoperator.md) retournée par l’opérateur.

## <a name="examples"></a>Exemples

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
