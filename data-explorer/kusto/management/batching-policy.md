---
title: Politique d’ingestionBatching - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique d’IngestionBatching dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 232767e390669a08312f10d3999d19264fb29f26
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744289"
---
# <a name="ingestionbatching-policy"></a>Politique d’ingestionBatching

La [politique d’ingestionBatching](batchingpolicy.md) est un objet de stratégie qui détermine quand l’agrégation de données doit cesser pendant l’ingestion de données selon les paramètres spécifiés.

La stratégie peut `null`être définie à , auquel cas les valeurs par défaut sont utilisées, en définissant la durée maximale de lotage à: 5 minutes, 1000 éléments et une taille totale de lot de 1G ou la valeur de cluster par défaut définie par Kusto.

Si la stratégie n’est pas définie pour une certaine entité, elle cherchera une politique de niveau de hiérarchie plus élevée, si tous sont configurés pour annuler la valeur par défaut sera utilisé. 

La stratégie a une limite inférieure de 10 secondes et il n’est pas recommandé d’utiliser des valeurs supérieures à 15 minutes.

## <a name="displaying-the-ingestionbatching-policy"></a>Affichage de la politique IngestionBatching

La stratégie peut être définie sur une base de données ou une table, et est affichée en utilisant l’une des commandes suivantes :

* `.show``database` *DatabaseName (en)* `policy``ingestionbatching`
* `.show``table` *DatabaseName*`.`*TableName* `policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>Modification de la politique d’ingestionBatching

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

Modification de la politique IngestionBatching pour plusieurs tables (dans le même contexte de base de données) :

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

Politique d’ingestionBatching :

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type`: table, base de données
* `database_or_table`: si l’entité est table ou base de données, son nom doit être spécifié dans la commande comme suit - 
  - `database_name` ou 
  - `database_name.table_name` ou 
  - `table_name`(lorsqu’elle est en cours d’exécution dans le contexte de la base de données spécifique)

## <a name="deleting-the-ingestionbatching-policy"></a>Suppression de la politique d’ingestionBatching

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
