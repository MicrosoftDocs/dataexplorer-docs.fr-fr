---
title: Commande de gestion de la stratégie Kusto IngestionBatching-Azure Explorateur de données
description: Cet article décrit la commande de stratégie IngestionBatching dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 04c59b33d780db1c9731ac71d1f905315afbc302
ms.sourcegitcommit: 4405ae34e119948778e0de5021077638d24da812
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86448042"
---
# <a name="ingestionbatching-policy-command"></a>Commande de stratégie IngestionBatching

La [stratégie ingestionBatching](batchingpolicy.md) est un objet de stratégie qui détermine à quel moment l’agrégation de données doit s’arrêter pendant l’ingestion des données en fonction des paramètres spécifiés.

La stratégie peut avoir la valeur `null` , auquel cas les valeurs par défaut sont utilisées, en affectant à la durée maximale de traitement par lot la valeur : 5 minutes, 1000 éléments et une taille de lot totale de 1g ou la valeur de cluster par défaut définie par Kusto.

Si la stratégie n’est pas définie pour une certaine entité, elle recherche une stratégie supérieure au niveau de la hiérarchie, si toutes sont définies sur null, la valeur par défaut est utilisée. 

La stratégie a une limite inférieure de 10 secondes et il n’est pas recommandé d’utiliser des valeurs supérieures à 15 minutes.

## <a name="displaying-the-ingestionbatching-policy"></a>Affichage de la stratégie IngestionBatching

La stratégie peut être définie sur une base de données ou une table, et est affichée à l’aide de l’une des commandes suivantes :

* `.show` `database` *DatabaseName* `policy` `ingestionbatching`
* `.show``table` *DatabaseName*, `.` *TableName* table `policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>Modification de la stratégie IngestionBatching

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

Modification de la stratégie IngestionBatching pour plusieurs tables (dans le même contexte de base de données) :

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

Stratégie IngestionBatching :

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type`: table, base de données
* `database_or_table`: si l’entité est une table ou une base de données, son nom doit être spécifié dans la commande comme suit : 
  - `database_name` ou 
  - `database_name.table_name` ou 
  - `table_name`(en cas d’exécution dans le contexte de la base de données spécifique)

## <a name="deleting-the-ingestionbatching-policy"></a>Suppression de la stratégie IngestionBatching

```kusto
.delete <entity_type> <database_or_table_name> policy ingestionbatching
```

**Exemples**

```kusto
// Show IngestionBatching policy for table `MyTable` in database `MyDatabase`
.show table MyDatabase.MyTable policy ingestionbatching 

// Set IngestionBatching policy on table `MyTable` (in database context) to batch ingress data by 30 seconds, 500 files, or 1GB (whatever comes first)
.alter table MyTable policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:30", "MaximumNumberOfItems": 500, "MaximumRawDataSizeMB": 1024}'

// Set IngestionBatching policy on multiple tables (in database context) to batch ingress data by 1 minute, 20 files, or 300MB (whatever comes first)
.alter tables (MyTable1, MyTable2, MyTable3) policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:01:00", "MaximumNumberOfItems": 20, "MaximumRawDataSizeMB": 300}'

// Delete IngestionBatching policy on table `MyTable`
.delete table MyTable policy ingestionbatching

// Delete IngestionBatching policy on database `MyDatabase`
.delete database MyDatabase policy ingestionbatching
```
