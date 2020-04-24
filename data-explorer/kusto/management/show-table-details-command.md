---
title: .afficher les détails de la table - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .show détails de table dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 6197e173df417acb1f5dc506337773f9534564bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519786"
---
# <a name="show-table-details"></a>.afficher les détails de la table
Renvoie un ensemble qui contient la table spécifiée ou toutes les tables de la base de données avec un résumé détaillé des propriétés de chaque table.

Nécessite la [permission du spectateur de base de données](../management/access-control/role-based-authorization.md).

```
.show table T1 details
.show tables (T1, ..., Tn) details
.show tables details
```

**Sortie**

| Paramètre de sortie           | Type     | Description                                                                                     |
|----------------------------|----------|-------------------------------------------------------------------------------------------------|
| `TableName`                | String   | Nom de la table.                                                                          |
| `DatabaseName`             | String   | La base de données à laquelle appartient la table.                                                         |
| `Folder`                   | String   | Le dossier de la table.                                                                             |
| `DocString`                | String   | Une chaîne documentant la table.                                                                 |
| `TotalExtents`             | Int64    | Le nombre total d’étendues dans le tableau.                                                       |
| `TotalExtentSize`          | Double   | La taille totale des étendues (taille comprimée et taille de l’index) dans le tableau.                          |
| `TotalOriginalSize`        | Double   | La taille totale d’origine des données dans le tableau.                                                   |
| `TotalRowCount`            | Int64    | Le nombre total de rangées dans le tableau.                                                          |
| `HotExtents`               | Int64    | Le nombre total d’étendues dans le tableau, stocké dans le cache chaud.                              |
| `HotExtentSize`            | Double   | La taille totale des étendues (taille comprimée et taille de l’index) dans le tableau, stockée dans le cache chaud. |
| `HotOriginalSize`          | Double   | La taille totale d’origine des données dans le tableau, stockée dans le cache chaud.                          |
| `HotRowCount`              | Int64    | Le nombre total de rangées dans la table, stockées dans le cache chaud.                                 |
| `AuthorizedPrincipals`     | String   | Les directeurs autorisés de la table, sérialisés sous le nom de JSON.                                          |
| `RetentionPolicy`          | String   | La politique de`*` conservation efficace de la table, sérialisée sous le titre JSON.                                  |
| `CachingPolicy`            | String   | La politique de`*` mise en cache efficace de la table, sérialisée sous le titre JSON.                                    |
| `ShardingPolicy`           | String   | La politique d’éclat efficace`*` de la table, sérialisée sous le titre JSON.666666666666666666                     |
| `MergePolicy`              | String   | La politique de`*` fusion efficace de la table, sérialisée sous le titre JSON.                                      |
| `StreamingIngestionPolicy` | String   | La politique efficace`*` d’ingestion de streaming de la table, sérialisée sous le titre JSON.                        |
| `MinExtentsCreationTime`   | DateTime | Le temps de création minimum d’une étendue dans la table (ou nul, s’il n’y a pas d’étendues).         |
| `MaxExtentsCreationTime`   | DateTime | Le temps de création maximum d’une étendue dans la table (ou nul, s’il n’y a pas d’étendues).         |
| `RowOrderPolicy`           | String   | La politique efficace de commande de ligne de la table, sérialisée comme JSON.                                     |

`*`*Compte tenu des politiques des entités mères (telles que la base de données/cluster).*

**Exemple de sortie**

| TableName         | nom_base_de_données | Dossier | DocString (en) | TotalExtents | TotalExtentSize | TotalOriginalSize TotalOriginalSize TotalOriginalSize TotalOri | TotalRowCount (en) | HotExtents (En anglais) | HotExtentSize | HotOriginalSize | HotRowCount (en) | Principes autorisés                                                                                                                                                                               | RétentionPolicy                                                                                                                                       | CachingPolicy                                                                        | ShardingPolicy (ShardingPolicy)                                                                    | FusionPolicy                                                                                                                                             | StreamingIngestionPolicy | MinExtentsCréationTime      | MaxExtentsCréationTime (en anglais)      |
|-------------------|--------------|--------|-----------|--------------|-----------------|-------------------|---------------|------------|---------------|-----------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-----------------------------|-----------------------------|
| Opérations        | Opérations   |        |           | 1164         | 37687203        | 53451358          | 223325        | 29         | 838752        | 1388213         | 5117        | ["Type": "AAD User", "DisplayName": "My Name alias@fabrikam.com(upn: )", "ObjectId": "a7a77777-4c21-4649-95c5-350bf486087b", "FQN": "aaduser-a7a777777-4c21-4649-95c5-350bf486087b", "Notes": ""] | "SoftDeletePeriod": "365.00:00:00","ContainerRecyclingPeriod": "1.00:00:00", "ExtentsDataSizeLimitInBytes": 0, "OriginalDataSizeLimitInBytes": 0  | "DataHotSpan": "4.00:00:00", "IndexHotSpan": "4.00:00:00", "ColumnOverrides": [] | " MaxRowCount ": 7500000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048 | " RowCountUpperBoundForMerge ": 0, "MaxExtentsToMerge": 100, "LoopPeriod": "01:00:00", "MaxRangeInHours": 3, "AllowRebuild": true, "AllowMerge": true ' | null                     |
| ServiceOperations | Opérations   |        |           | 1109         | 76588803        | 91553069          | 110125        | 27         | 2635742       | 2929926         | 3162        | ["Type": "AAD User", "DisplayName": "My Name alias@fabrikam.com(upn: )", "ObjectId": "a7a77777-4c21-4649-95c5-350bf486087b", "FQN": "aaduser-a7a777777-4c21-4649-95c5-350bf486087b", "Notes": ""] | "SoftDeletePeriod": "365.00:00:00","ContainerRecyclingPeriod": "1.00:00:00", "ExtentsDataSizeLimitInBytes": 0, "OriginalDataSizeLimitInBytes": 0 | "DataHotSpan": "4.00:00:00", "IndexHotSpan": "4.00:00:00", "ColumnOverrides": [] | " MaxRowCount ": 7500000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048 | " RowCountUpperBoundForMerge ": 0, "MaxExtentsToMerge": 100, "LoopPeriod": "01:00:00", "MaxRangeInHours": 3, "AllowRebuild": true, "AllowMerge": true ' | null                     | 2018-02-08 15:30:38.8489786 | 2018-02-14 07:47:28.7660267 |