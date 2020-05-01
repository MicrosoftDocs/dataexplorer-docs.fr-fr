---
title: Réponse HTTP de requête v2-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la réponse HTTP de la requête v2 dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 86a56d77005b2c6b5c9d38bbec85eebfbcb481dc
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617899"
---
# <a name="query-v2-http-response"></a>Réponse HTTP de la requête v2

Si le code d’État est 200, le corps de la réponse est un tableau JSON.
Chaque objet JSON du tableau est appelé _Frame_.

Il existe plusieurs types de frames :

* [DataSetHeader](#datasetheader)
* [TableHeader](#tableheader)
* [TableFragment](#tablefragment)
* [TableProgress](#tableprogress)
* [TableCompletion](#tablecompletion)
* [Tables](#datatable)
* [DataSetCompletion](#datasetcompletion)

## <a name="datasetheader"></a>DataSetHeader 

Le `DataSetHeader` frame est toujours le premier dans le jeu de données et apparaît exactement une fois.

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

Où :

* `Version`version du protocole. La version actuelle est `v2.0`.
* `IsProgressive`indicateur booléen qui indique si ce jeu de données contient des trames progressives. 
   Une image progressive est l’une des suivantes :
   
     | Frame             | Description                                    |
     |-------------------| -----------------------------------------------|
     | `TableHeader`     | Contient des informations générales sur la table.   |
     | `TableFragment`   | Contient un partition de données rectangulaire de la table |
     | `TableProgress`   | Contient la progression en pourcentage (0-100)       |
     | `TableCompletion` | Indique que ce frame est le dernier      |
        
    Les frames ci-dessus décrivent une table.
    Si l' `IsProgressive` indicateur n’a pas la valeur true, chaque table de l’ensemble est sérialisée à l’aide d’un seul Frame :
* `DataTable`: Contient toutes les informations dont le client a besoin sur une seule table dans le jeu de données.

## <a name="tableheader"></a>TableHeader

Les requêtes effectuées avec l’option `results_progressive_enabled` ayant la valeur true peuvent inclure ce frame. À la suite de ce tableau, les clients peuvent s’attendre `TableFragment` à `TableProgress` une séquence d’entrelacement des frames et. Le cadre final de la table est `TableCompletion`.

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

Où :

* `TableId`ID unique de la table.
* `TableKind`est l’un des éléments suivants :

    * PrimaryResult
    * QueryCompletionInformation
    * QueryTraceLog
    * QueryPerfLog
    * TableOfContents
    * QueryProperties
    * QueryPlan
    * Unknown
      
* `TableName`nom de la table.
* `Columns`est un tableau décrivant le schéma de la table.

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

Les types de colonnes pris en charge sont décrits [ici](../../query/scalar-data-types/index.md).

## <a name="tablefragment"></a>TableFragment

Le `TableFragment` frame contient un fragment de données rectangulaires de la table. En plus des données réelles, ce frame contient également une `TableFragmentType` propriété qui indique au client ce qu’il faut faire avec le fragment. Le fragment est ajouté aux fragments existants ou les remplace.

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

Où :

* `TableId`ID unique de la table.
* `FieldCount`nombre de colonnes de la table.
* `TableFragmentType`décrit ce que le client doit faire avec ce fragment. 
    `TableFragmentType`est l’un des éléments suivants :
    
    * DataAppend
    * DataReplace
      
* `Rows`est un tableau à deux dimensions qui contient les données du fragment.

## <a name="tableprogress"></a>TableProgress

Le `TableProgress` Frame peut entrelacer le `TableFragment` Frame décrit ci-dessus.
Son seul but est d’informer le client de la progression de la requête.

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

Où :

* `TableId`ID unique de la table.
* `TableProgress`progression en pourcentage (0--100).

## <a name="tablecompletion"></a>TableCompletion

Le `TableCompletion` cadre marque la fin de la transmission de la table. Aucun autre frame lié à cette table ne sera envoyé.

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

Où :

* `TableId`ID unique de la table.
* `RowCount`nombre total de lignes dans la table.

## <a name="datatable"></a>DataTable

Les requêtes émises avec l' `EnableProgressiveQuery` indicateur défini sur false n’incluent pas les frames`TableHeader`( `TableFragment`, `TableProgress`, et `TableCompletion`). Au lieu de cela, chaque table du jeu de données est transmise à l’aide du `DataTable` frame qui contient toutes les informations dont le client a besoin pour lire la table.

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

* `TableId`ID unique de la table.
* `TableKind`est l’un des éléments suivants :

    * PrimaryResult
    * QueryCompletionInformation
    * QueryTraceLog
    * QueryPerfLog
    * QueryProperties
    * QueryPlan
    * Unknown
      
* `TableName`nom de la table.
* `Columns`est un tableau qui décrit le schéma de la table et comprend les éléments suivants :

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

* `Rows`est un tableau à deux dimensions qui contient les données de la table.

### <a name="the-meaning-of-tables-in-the-response"></a>Signification des tables dans la réponse

* `PrimaryResult`: Résultat tabulaire principal de la requête. Pour chaque [instruction d’expression tabulaire](../../query/tabularexpressionstatements.md), une ou plusieurs tables sont générées dans l’ordre, représentant les résultats produits par l’instruction. Il peut y avoir plusieurs tables de ce type en raison des [lots](../../query/batches.md) et des [opérateurs de fourche](../../query/forkoperator.md).
* `QueryCompletionInformation`-Fournit des informations supplémentaires sur l’exécution de la requête, par exemple si elle s’est terminée avec succès ou non, et quelles sont les ressources consommées par la requête (semblable à la table QueryStatus dans la réponse v1). 
* `QueryProperties`-Fournit des valeurs supplémentaires telles que des instructions de visualisation du client (émises, par exemple, pour refléter les informations dans l' [opérateur Render](../../query/renderoperator.md)) et des informations sur le [curseur de la base de données](../../management/databasecursor.md) ).
* `QueryTraceLog`-Les informations du journal de suivi des performances `perftrace` (renvoyées lorsque dans les propriétés de la [demande du client](../netfx/request-properties.md) ont la valeur true).

## <a name="datasetcompletion"></a>DataSetCompletion

Le `DataSetCompletion` frame est le dernier dans le jeu de données.

```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

Où :

* `HasErrors`a la valeur true si des erreurs se sont produites lors de la génération du jeu de données.
* `Cancelled`a la valeur true si la requête qui a conduit à la génération du jeu de données a été annulée avant d’être terminée. 
* `OneApiErrors`est retourné uniquement si `HasErrors` a la valeur true. Pour obtenir une description du `OneApiErrors` format, consultez la section 7.10.2 [ici](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md).
