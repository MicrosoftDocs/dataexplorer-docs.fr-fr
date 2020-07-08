---
title: . déplacer des extensions-Azure Explorateur de données
description: Cet article décrit la commande déplacer les extensions dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 994f7077b1583317320cc561b2d5a5ae1cbe829f
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060631"
---
# <a name="move-extents"></a>. déplacer des étendues

Cette commande s’exécute dans le contexte d’une base de données spécifique. Il déplace les étendues spécifiées de la table source vers la table de destination.

La commande nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) pour les tables source et de destination.

> [!NOTE]
> Les partitions de données sont des **Extensions** appelées dans Kusto, et toutes les commandes utilisent « extent » ou « extents » comme synonyme.
> Pour plus d’informations sur les extensions, consultez [extents (Data partitions) Overview](extents-overview.md).

## <a name="syntax"></a>Syntax

`.move`[ `async` ] `extents` `all` `from` `table` *SourceTableName* `to` `table` *DestinationTableName*

`.move`[ `async` ] `extents` `(` *Guid1* [ `,` *GUID2* ...] `)` `from` `table` *SourceTableName* `to` `table` *DestinationTableName* 

`.move`[ `async` ] `extents` `to` `table` *DestinationTableName*  <|  *Requête* DestinationTableName

`async`(facultatif). Exécutez la commande de façon asynchrone. 
   * Un ID d’opération (Guid) est retourné.
   * L’état de l’opération peut être surveillé. Utilisez la commande [. Show Operations](operations.md#show-operations) .
   * Les résultats d’une exécution réussie peuvent être récupérés. Utilisez la commande [. afficher les détails](operations.md#show-operation-details) de l’opération.

Il existe trois façons de spécifier les étendues à déplacer :
* Déplacer toutes les étendues d’une table spécifique.
* Spécifiez les ID d’étendue de manière explicite dans la table source.
* Fournissez une requête dont les résultats spécifient les ID d’étendue dans les tables sources.

## <a name="restrictions"></a>Restrictions

* Les tables source et de destination doivent être dans la base de données de contexte.
* Toutes les colonnes de la table source sont censées exister dans la table de destination avec le même nom et le même type de données.

## <a name="specify-extents-with-a-query"></a>Spécifier des étendues à l’aide d’une requête

```kusto
.move extents to table TableName <| ...query...
```

Les étendues sont spécifiées à l’aide d’une requête Kusto qui retourne un jeu d’enregistrements avec une colonne appelée *ExtentId*.

## <a name="return-output-for-sync-execution"></a>Sortie de retour (pour l’exécution de la synchronisation)

Paramètre de sortie |Type |Description
---|---|---
OriginalExtentId |string |Identificateur unique (GUID) de l’étendue d’origine dans la table source, qui a été déplacée vers la table de destination.
ResultExtentId |string |Identificateur unique (GUID) de l’étendue résultante qui a été déplacée de la table source vers la table de destination. En cas d’échec-« échec ».
Détails |string |Contient les détails de l’échec, en cas d’échec de l’opération.

## <a name="examples"></a>Exemples

### <a name="move-all-extents"></a>Déplacer toutes les étendues 

Déplacer toutes les étendues de la table `MyTable` vers la table `MyOtherTable` :

```kusto
.move extents all from table MyTable to table MyOtherTable
```

### <a name="move-two-specific-extents"></a>Déplacer deux étendues spécifiques 

Déplacez deux extensions spécifiques (par ID d’étendue) de la table `MyTable` vers la table `MyOtherTable` :

```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

### <a name="move-all-extents-from-specific-tables"></a>Déplacer toutes les étendues à partir de tables spécifiques 

Déplacer toutes les étendues d’un tavles spécifique ( `MyTable1` , `MyTable2` ) vers la table `MyOtherTable` :

```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

## <a name="sample-output"></a>Exemple de sortie

|OriginalExtentId |ResultExtentId| Détails
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 
