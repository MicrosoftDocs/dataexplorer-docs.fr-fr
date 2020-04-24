---
title: schema_merge plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit schema_merge plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 67326a0e7a92d064613ee3a3de2851addb502fc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509246"
---
# <a name="schema_merge-plugin"></a>schema_merge plugin

Fusionne les définitions de schémas tabulaires en schéma unifié. 

Les définitions de schéma sont censées être en format comme produit par [l’opérateur getschema.](./getschemaoperator.md)

Les colonnes des syndicats de fusion de schémas qui apparaissent dans les schémas d’entrée et tentent de réduire les types de données à un type de données commun (au cas où les types de données ne peuvent pas être réduits, une erreur est montrée sur une colonne problématique).

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

**Syntaxe**

`T`PreserveOrder *(preserveOrder)* `|` `evaluate` `schema_merge(``)`

**Arguments**

* *PreserveOrder*: (Facultatif) Lorsqu’il est réglé, `true`dirige vers le plugin pour valider cet ordre de colonne tel que défini par le premier schéma tabulaire est conservé. En d’autres termes, si la même colonne apparaît dans plusieurs schémas, la colonne ordinaire doit être comme dans le premier schéma, il est apparu. La valeur par défaut est `true`.

**Retourne**

Le `schema_merge` plugin retourne simiar sortie à ce que [l’opérateur getschema](./getschemaoperator.md) retourne.

**Exemples**

Fusionnez avec un schéma qui a une nouvelle colonne jointe:

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

Fusion avec un schéma qui a`HttpStatus` la commande de `1` `2` colonne différente (changements ordinaux de dans la nouvelle variante):

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
|httpStatus|-1|ERROR (type CSL inconnu: ERROR (les colonnes sont hors d’ordre))|ERROR (les colonnes sont hors d’ordre)|

Fusionnez avec un schéma qui a la `PreserveOrder` commande `false` de colonne différente, mais avec réglé à ce temps:

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