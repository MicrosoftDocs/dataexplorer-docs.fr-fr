---
title: . afficher les détails de la table-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. afficher les détails d’une table dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3182c059a3f56b94950009672759388bbff8058d
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616913"
---
# <a name="show-table-details"></a>.show table details
Retourne un jeu qui contient la table spécifiée ou toutes les tables de la base de données avec un résumé détaillé des propriétés de chaque table.

Nécessite l' [autorisation de la visionneuse de base de données](../management/access-control/role-based-authorization.md).

```kusto
.show table T1 details
.show tables (T1, ..., Tn) details
.show tables details
```

**Sortie**

| Paramètre de sortie           | Type     | Description                                                                                     |
|----------------------------|----------|-------------------------------------------------------------------------------------------------|
| `TableName`                | String   | Nom de la table.                                                                          |
| `DatabaseName`             | String   | Base de données à laquelle la table appartient.                                                         |
| `Folder`                   | String   | Dossier de la table.                                                                             |
| `DocString`                | String   | Chaîne qui documente la table.                                                                 |
| `TotalExtents`             | Int64    | Nombre total d’étendues dans la table.                                                       |
| `TotalExtentSize`          | Double   | Taille totale des étendues (taille compressée + taille de l’index) dans la table.                          |
| `TotalOriginalSize`        | Double   | Taille totale d’origine des données dans la table.                                                   |
| `TotalRowCount`            | Int64    | Nombre total de lignes dans la table.                                                          |
| `HotExtents`               | Int64    | Nombre total d’étendues dans la table, stockées dans le cache à chaud.                              |
| `HotExtentSize`            | Double   | Taille totale des étendues (taille compressée + taille de l’index) dans la table, stockée dans le cache à chaud. |
| `HotOriginalSize`          | Double   | Taille totale d’origine des données dans la table, stockées dans le cache à chaud.                          |
| `HotRowCount`              | Int64    | Nombre total de lignes de la table, stockées dans le cache à chaud.                                 |
| `AuthorizedPrincipals`     | String   | Principaux autorisés de la table, sérialisés au format JSON.                                          |
| `RetentionPolicy`          | String   | La stratégie de rétention effective`*` de la table, sérialisée au format JSON.                                  |
| `CachingPolicy`            | String   | Stratégie de mise en`*` cache effective de la table, sérialisée au format JSON.                                    |
| `ShardingPolicy`           | String   | La stratégie partitionnement effective`*` de la table, sérialisée au format JSON. 66666666666666                     |
| `MergePolicy`              | String   | Stratégie de fusion effective`*` de la table, sérialisée au format JSON.                                      |
| `StreamingIngestionPolicy` | String   | La stratégie d’ingestion de streaming efficace`*` de la table, sérialisée au format JSON.                        |
| `MinExtentsCreationTime`   | DateTime | Heure de création minimale d’une étendue dans la table (ou null, s’il n’y a pas d’étendues).         |
| `MaxExtentsCreationTime`   | DateTime | Heure de création maximale d’une étendue dans la table (ou null, s’il n’y a pas d’étendues).         |
| `RowOrderPolicy`           | String   | La stratégie d’ordre des lignes effective de la table, sérialisée au format JSON.                                     |

`*`*En tenant compte des stratégies d’entités parentes (par exemple, base de données/cluster).*

**Exemple de sortie**

| TableName         | nom_base_de_données | Dossier | DocString | TotalExtents | TotalExtentSize | TotalOriginalSize | TotalRowCount | HotExtents | HotExtentSize | HotOriginalSize | HotRowCount | AuthorizedPrincipals                                                                                                                                                                               | RetentionPolicy                                                                                                                                       | CachingPolicy                                                                        | ShardingPolicy                                                                    | MergePolicy                                                                                                                                             | StreamingIngestionPolicy | MinExtentsCreationTime      | MaxExtentsCreationTime      |
|-------------------|--------------|--------|-----------|--------------|-----------------|-------------------|---------------|------------|---------------|-----------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-----------------------------|-----------------------------|
| Opérations        | Opérations   |        |           | 1164         | 37687203        | 53451358          | 223325        | 29         | 838752        | 1388213         | 5117        | [{« Type » : « AAD User », « DisplayName » : « My Name (UPN : alias@fabrikam.com) », « ObjectID » : « a7a77777-4c21-4649-95c5-350bf486087b », « FQN » : « aaduser = a7a77777-4c21-4649-95c5-350bf486087b », « notes » : «»}] | {"SoftDeletePeriod" : "365.00:00:00", "ContainerRecyclingPeriod" : "1.00:00:00", "ExtentsDataSizeLimitInBytes" : 0, "OriginalDataSizeLimitInBytes" : 0}  | {"DataHotSpan" : "4.00:00:00", "IndexHotSpan" : "4.00:00:00", "ColumnOverrides" : []} | {"MaxRowCount" : 750000, "MaxExtentSizeInMb" : 1024, "MaxOriginalSizeInMb" : 2048} | {"RowCountUpperBoundForMerge" : 0, "MaxExtentsToMerge" : 100, "LoopPeriod" : "01:00:00", "MaxRangeInHours" : 3, "AllowRebuild" : true, "AllowMerge" : true} | null                     |
| ServiceOperations | Opérations   |        |           | 1109         | 76588803        | 91553069          | 110125        | 27         | 2635742       | 2929926         | 3162        | [{« Type » : « AAD User », « DisplayName » : « My Name (UPN : alias@fabrikam.com) », « ObjectID » : « a7a77777-4c21-4649-95c5-350bf486087b », « FQN » : « aaduser = a7a77777-4c21-4649-95c5-350bf486087b », « notes » : «»}] | {"SoftDeletePeriod" : "365.00:00:00", "ContainerRecyclingPeriod" : "1.00:00:00", "ExtentsDataSizeLimitInBytes" : 0, "OriginalDataSizeLimitInBytes" : 0} | {"DataHotSpan" : "4.00:00:00", "IndexHotSpan" : "4.00:00:00", "ColumnOverrides" : []} | {"MaxRowCount" : 750000, "MaxExtentSizeInMb" : 1024, "MaxOriginalSizeInMb" : 2048} | {"RowCountUpperBoundForMerge" : 0, "MaxExtentsToMerge" : 100, "LoopPeriod" : "01:00:00", "MaxRangeInHours" : 3, "AllowRebuild" : true, "AllowMerge" : true} | null                     | 2018-02-08 15:30:38.8489786 | 2018-02-14 07:47:28.7660267 |
