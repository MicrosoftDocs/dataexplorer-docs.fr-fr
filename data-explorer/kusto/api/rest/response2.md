---
title: Réponse de Requête V2 HTTP - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit La réponse de Query V2 HTTP dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: cca9b8381c7c59993c1e9071c46f34c1754d2429
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524308"
---
# <a name="query-v2-http-response"></a>Réponse de Requête V2 HTTP

Si le code d’état est de 200, le corps de réponse est un tableau JSON.
Chaque objet JSON dans le tableau est appelé un _cadre_.

Il existe 7 types d’images :

1. DataSetHeader (en anglais)
2. Tête de table
3. TableFragment
4. TableProgresse
5. TableCompletion
6. DataTable
7. DataSetCompletion

## <a name="datasetheader"></a>DataSetHeader (en anglais) 

C’est toujours la première image dans l’ensemble de données et apparaît exactement une fois.

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

Où :

1. `Version`détient la version du protocole. La version actuelle est `v2.0`.
2. `IsProgressive`est un drapeau boolean qui indique si cet ensemble de données contient des cadres progressifs. Un cadre progressif est l’un des éléments suivants :
    1. `TableHeader`: Contient des informations générales sur la table
    2. `TableFragment`: Contient un éclat de données rectangluar de la table
    3. `TableProgress`: Contient les progrès en pourcentage (0-100)
    4. `TableCompletion`: Marques que c’est le dernier cadre de la table.
        
    Les quatre cadres ci-dessus sont utilisés ensemble pour décrire une table.
    Si ce drapeau n’est pas réglé, alors chaque table de l’ensemble sera sérialisée à l’aide d’un seul cadre :
      1. `DataTable`: Contient toutes les informations dont le client a besoin sur un seul tableau dans l’ensemble de données.


## <a name="tableheader"></a>Tête de table

Les requêtes qui `results_progressive_enabled` sont émises avec l’option définie pour être vraies peuvent inclure ce cadre. Après ce tableau, les clients doivent `TableFragment` s’attendre à une séquence entrelacée de et `TableProgress` des cadres, suivie d’un `TableCompletion` cadre. Après quoi, plus aucun cadre lié à cette table ne sera envoyé.

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

Où :

1. `TableId`est l’id unique de la table.
2. `TableKind`est le genre de la table, qui peut être l’un des éléments suivants:

      * PrimaireResult
      * Information sur la conformité
      * QueryTraceLog (en)
      * QueryPerfLog (en)
      * TableOfContents
      * QueryProperties
      * Plan de requête
      * Unknown
3. `TableName`est le nom de la table.
4. `Columns`est un tableau décrivant le schéma de la table:

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
Les types de colonnes prises en charge sont décrits [ici](../../query/scalar-data-types/index.md).

## <a name="tablefragment"></a>TableFragment

Ce cadre contient un fragment de données rectangulaires de la table. En plus des données réelles, `TableFragmentType` ce cadre contient une propriété, qui indique au client ce qu’il faut faire avec le fragment (il peut soit être annexé à des fragments existants, ou les remplacer tous ensemble).

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

Où :

1. `TableId`est l’id unique de la table.
2. `FieldCount`est le nombre de colonnes dans le tableau
3. `TableFragmentType`décrit ce que le client devrait faire avec ce fragment. Il peut s'agir d'une des méthodes suivantes :
      * DataAppend (en)
      * DataReplace (en)
4. `Rows`est un tableau bidimensionnel qui contient les données fragment.

## <a name="tableprogress"></a>TableProgresse

Ce cadre peut entrelacer avec le `TableFragment` cadre décrit ci-dessus.
Le seul but est d’informer le client sur les progrès de la requête.

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

Où :

1. `TableId`est l’id unique de la table.
2. `TableProgress`est le progrès en pourcentage (0--100).

## <a name="tablecompletion"></a>TableCompletion

Les `TableCompletion` cadres marquent la fin de la transmission de la table. Plus aucun cadre lié à cette table ne sera envoyé.

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

Où :

1. `TableId`est l’id unique de la table.
2. `RowCount`est le nombre final de rangées dans le tableau.

## <a name="datatable"></a>DataTable

Les requêtes qui `EnableProgressiveQuery` sont émises avec le drapeau mis à faux`TableHeader` `TableFragment`n’incluront aucun des 4 cadres précédents ( , `TableProgress` , et `TableCompletion`). Au lieu de cela, chaque tableau de l’ensemble de données sera transmis à l’aide d’un seul cadre, le `DataTable` cadre, qui contient toutes les informations dont le client a besoin pour lire la table.

```json
{
    "TableId": Number,

    "TableKind": string,

    "TableName": string,

    "Columns": Array,

    "Rows": Array,
}
```    

Où :

1. `TableId`est l’id unique de la table.
2. `TableKind`est le genre de la table, qui peut être l’un des éléments suivants:

      * PrimaireResult
      * Information sur la conformité
      * QueryTraceLog (en)
      * QueryPerfLog (en)
      * QueryProperties
      * Plan de requête
      * Unknown
3. `TableName`est le nom de la table.
4. `Columns`est un tableau décrivant le schéma de la table:

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
4. `Rows`est un tableau bidimensionnel qui contient les données de la table.

### <a name="the-meaning-of-tables-in-the-response"></a>Le sens des tableaux dans la réponse

* `PrimaryResult`- Le principal résultat tabulaire de la requête. Pour chaque [énoncé d’expression tabulaire,](../../query/tabularexpressionstatements.md)un ou plusieurs tableaux sont émis en ordre, représentant les résultats produits par l’énoncé (il peut y avoir plusieurs tableaux de ce type en raison de lots et [d’opérateurs](../../query/forkoperator.md)de fourchettes). [batches](../../query/batches.md)
* `QueryCompletionInformation`- fournit des informations supplémentaires sur l’exécution de la requête elle-même, telles que la question de savoir si elle a été complétée avec succès ou non, et quelles étaient les ressources consommées par la requête (semblable au tableau QueryStatus dans la réponse v1). 
* `QueryProperties`- fournit des valeurs supplémentaires telles que les instructions de visualisation du client (émis, par exemple, pour refléter les informations dans [l’opérateur de rendu](../../query/renderoperator.md)) et les informations [de curseur de base de données).](../../management/databasecursor.md)
* `QueryTraceLog`- informations de journal de trace de performance (retourné lors de la mise en place de perftrace dans [les propriétés de demande client](../netfx/request-properties.md)).

## <a name="datasetcompletion"></a>DataSetCompletion

Il s’agit du cadre final de l’ensemble de données.
```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

Où :

1. `HasErrors`est vrai si il y avait des erreurs générant l’ensemble de données.
2. `Cancelled`est vrai si la demande qui a conduit à la génération de l’ensemble de données a été annulée à mi-chemin. 
3. `OneApiErrors`ne se `HasErrors` transmet que si c’est vrai. Pour une description `OneApiErrors` du format, voir l’article 7.10.2 [ici](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md).