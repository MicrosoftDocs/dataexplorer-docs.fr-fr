---
title: . remplacement des extensions-Azure Explorateur de données
description: Cet article décrit la commande remplacer les extensions dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: cd193cb370136fd7f14a8892f157a895a1d7ad50
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060668"
---
# <a name="replace-extents"></a>. remplacement des extensions

Cette commande s’exécute dans le contexte d’une base de données spécifique.
Il déplace les étendues spécifiées de leurs tables sources vers la table de destination, puis supprime les étendues spécifiées de la table de destination.
Toutes les opérations de déplacement et de déplacement sont effectuées dans une transaction unique.

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) pour les tables source et de destination.

> [!NOTE]
> Les partitions de données sont des **Extensions** appelées dans Kusto, et toutes les commandes utilisent « extent » ou « extents » comme synonyme.
> Pour plus d’informations sur les extensions, consultez [extents (Data partitions) Overview](extents-overview.md).

## <a name="syntax"></a>Syntax

`.replace`[ `async` ] `extents` `in` `table` *DestinationTableName* `<| 
{` *Requête DestinationTableName pour les étendues à supprimer de*la requête de table `},{` *pour les étendues à déplacer vers la table*`}`

* `async`(facultatif) : exécutez la commande de façon asynchrone.
    * Un ID d’opération (Guid) est retourné.
    * L’état de l’opération peut être surveillé. Utilisez la commande [. Show Operations](operations.md#show-operations) .
    * Les résultats d’une exécution réussie peuvent être récupérés. Utilisez la commande [. afficher les détails](operations.md#show-operation-details) de l’opération.

Pour spécifier les étendues qui doivent être supprimées ou déplacées, utilisez l’une des deux requêtes suivantes :
* *requête d’étendues à supprimer de la table*: les résultats de cette requête spécifient les ID d’étendue à supprimer de la table de destination.
* *requête pour les étendues à déplacer vers la table*: les résultats de cette requête spécifient les ID d’étendue dans les tables sources qui doivent être déplacées vers la table de destination.

Les deux requêtes doivent retourner un jeu d’enregistrements avec une colonne appelée « ExtentId ».

## <a name="restrictions"></a>Restrictions

* Les tables source et de destination doivent être dans la base de données de contexte.
* Toutes les étendues spécifiées par la *requête pour les étendues à supprimer de la table* sont censées appartenir à la table de destination.
* Toutes les colonnes des tables sources sont supposées exister dans la table de destination avec le même nom et le même type de données.

## <a name="return-output-for-sync-execution"></a>Sortie de retour (pour l’exécution de la synchronisation)

Paramètre de sortie |Type |Description
---|---|---
OriginalExtentId |string |Identificateur unique (GUID) de l’étendue d’origine dans la table source qui a été déplacée vers la table de destination, ou l’étendue de la table de destination qui a été supprimée.
ResultExtentId |string |Identificateur unique (GUID) de l’étendue résultante qui a été déplacée de la table source vers la table de destination. Vide si l’étendue a été supprimée de la table de destination. En cas d’échec : « échec ».
Détails |string |Indique les détails de l’échec en cas d’échec de l’opération.

> [!NOTE]
> La commande échoue si les étendues retournées par les *étendues à supprimer de* la requête de table n’existent pas dans la table de destination. Cela peut se produire si les étendues ont été fusionnées avant l’exécution de la commande Replace.
> Pour vous assurer que la commande échoue sur les extensions manquantes, vérifiez que la requête retourne le ExtentIds attendu. L’exemple de #1 ci-dessous échoue si l’étendue à supprimer n’existe pas dans la table *MyOtherTable*. Toutefois, l’exemple de #2 réussit même si l’étendue à supprimer n’existe pas, puisque la requête à supprimer n’a retourné aucun ID d’étendue.

## <a name="examples"></a>Exemples

### <a name="move-all-extents-from-two-tables"></a>Déplacer toutes les étendues de deux tables 

Déplacer toutes les étendues de deux tables spécifiques ( `MyTable1` , `MyTable2` ) vers la table `MyOtherTable` et supprimer toutes les étendues dans les `MyOtherTable` balises avec `drop-by:MyTag` :

```kusto
.replace extents in table MyOtherTable <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

#### <a name="sample-output"></a>Exemple de sortie

|OriginalExtentId |ResultExtentId |Détails
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

### <a name="move-all-extents-from-one-table-to-another-drop-specific-extent"></a>Déplacer toutes les étendues d’une table vers une autre, supprimer une étendue spécifique

Déplacer toutes les étendues d’un tavle spécifique ( `MyTable1` ) à `MyOtherTable` la table, et supprimer une étendue spécifique dans `MyOtherTable` , à l’aide de son ID :

```kusto
.replace extents in table MyOtherTable <|
    {
        print ExtentId = "2cca5844-8f0d-454e-bdad-299e978be5df"
    },
    {
        .show table MyTable1 extents 
    }
```

```kusto
.replace extents in table MyOtherTable  <|
    {
        .show table MyOtherTable extents
        | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) 
    },
    {
        .show table MyTable1 extents 
    }
```

### <a name="implement-an-idempotent-logic"></a>Implémenter une logique idempotent

Implémentez une logique idempotent afin que Kusto supprime les étendues de la table `t_dest` uniquement s’il existe des étendues à déplacer de la table `t_source` vers la table `t_dest` :

```kusto
.replace async extents in table t_dest <|
{
    let any_extents_to_move = toscalar( 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize count() > 0
    );
    let extents_to_drop =
        t_dest
        | where any_extents_to_move and extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_drop
},
{
    let extents_to_move = 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_move
}
```
