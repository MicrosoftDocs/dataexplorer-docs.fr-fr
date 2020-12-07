---
title: . supprimer des balises d’étendue-Azure Explorateur de données
description: Cet article décrit la commande de balises de suppression d’étendue dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 45e7d0abf42e613a9d197371dcc374fe4ac11fed
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321010"
---
# <a name="drop-extent-tags"></a>.drop extent tags

La commande s’exécute dans le contexte d’une base de données spécifique. Elle supprime les [balises d’étendue](extents-overview.md#extent-tagging) spécifiques de toutes les étendues ou des extensions spécifiques de la base de données et de la table.  

> [!NOTE]
> Les partitions de données sont des **Extensions** appelées dans Kusto, et toutes les commandes utilisent « extent » ou « extents » comme synonyme.
> Pour plus d’informations sur les extensions, consultez [extents (Data partitions) Overview](extents-overview.md).

Il existe deux façons de spécifier les balises à supprimer des extensions :

* Spécifiez explicitement les balises qui doivent être supprimées de toutes les étendues de la table spécifiée.
* Fournissez une requête dont les résultats spécifient les ID d’étendue dans la table et, pour chaque étendue, les balises qui doivent être supprimées.

## <a name="syntax"></a>Syntaxe

`.drop` [ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*: étiquette1*' [ `,` '*tag2*' `,` ... `,` ' *TagN*']`)`

`.drop`[ `async` ] `extent` `tags`  <|  *requête*

`async` (facultatif) : exécutez la commande de façon asynchrone.
   * Un ID d’opération (Guid) est retourné.
   * L’état de l’opération peut être surveillé. Utilisez la [`.show operations`](operations.md#show-operations) commande.
   * Utilisez la [`.show operation details`](operations.md#show-operation-details) commande pour récupérer les résultats d’une exécution réussie.

## <a name="restrictions"></a>Restrictions

Toutes les étendues doivent être dans la base de données de contexte et doivent appartenir à la même table.

## <a name="specify-extents-with-a-query"></a>Spécifier des étendues à l’aide d’une requête

Les étendues et les balises à supprimer sont spécifiées à l’aide d’une requête Kusto. Elle retourne un jeu d’enregistrements avec une colonne appelée « ExtentId » et une colonne appelée « Tags ».

> [!NOTE]
> Lorsque vous utilisez la [bibliothèque cliente .net Kusto](../api/netfx/about-kusto-data.md), les méthodes suivantes génèrent la commande requise :
> * `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
> * `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) pour toutes les tables source et de destination impliquées.

### <a name="syntax-for-drop-extent-tags-in-query"></a>Syntaxe pour les balises d’extension de suppression dans la requête

```kusto 
.drop extent tags <| ...query...
```

### <a name="return-output"></a>Sortie de retour

Paramètre de sortie |Type |Description 
---|---|---
OriginalExtentId |string |Identificateur unique (GUID) de l’extension d’origine dont les balises ont été modifiées. L’étendue est supprimée dans le cadre de l’opération.
ResultExtentId |string |Identificateur unique (GUID) pour l’étendue de résultat qui a modifié des balises. L’extension est créée et ajoutée dans le cadre de l’opération. En cas d’échec-« échec ».
ResultExtentTags |string |Collection de balises avec lesquelles l’étendue du résultat est marquée, le cas échéant, ou « null » en cas d’échec de l’opération.
Détails |string |Indique les détails de l’échec en cas d’échec de l’opération.

## <a name="examples"></a>Exemples

### <a name="drop-one-tag"></a>Supprimer une balise

Supprimez la `drop-by:Partition000` balise de l’une des étendues de la table avec laquelle elle est marquée :

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

### <a name="drop-several-tags"></a>Supprimer plusieurs balises

Déposez les balises `drop-by:20160810104500` , `a random tag` et `drop-by:20160810` de toute extension dans la table qui est marquée avec l’une ou l’autre des deux :

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

### <a name="drop-all-drop-by-tags"></a>Supprimer toutes les `drop-by` balises 

Supprimer toutes les `drop-by` balises des étendues dans la table `MyTable` :

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

### <a name="drop-all-tags-matching-specific-regex"></a>Supprimer toutes les balises correspondant à une expression régulière spécifique 

Supprimer toutes les balises correspondant `drop-by:StreamCreationTime_20160915(\d{6})` à Regex des étendues dans la table `MyTable` :

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

## <a name="sample-output"></a>Exemple de sortie

|OriginalExtentId |ResultExtentId | ResultExtentTags | Détails
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |
