---
title: Gestion des politiques de sharding - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des politiques Sharding dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7770e0834e4b00f42158732e667d41eb636cec5b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520041"
---
# <a name="sharding-policy-management"></a>Gestion des politiques enhardissante

## <a name="show-policy"></a>montrer la politique

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`affiche la politique d’enhardisage de la base de données ou de la table. Il affiche toutes les stratégies du type d’entité donnée (base de données ou tableau) si le nom donné est ''.

### <a name="output"></a>Output

|Nom de stratégie | Nom de l’entité | Stratégie | Entités enfants | Type d'entité
|---|---|---|---|---
|ExtentsShardingPolicy (en) | base de données / nom de table | json format chaîne qui représente la politique | liste des tableaux (pour une base de données)|base de données / table

## <a name="alter-policy"></a>modifier la politique

### <a name="examples"></a>Exemples

Les exemples suivants renvoient la politique d’enhardisement des étendues mises à jour pour l’entité, avec une base de données ou un tableau spécifié comme nom qualifié, comme leur sortie.

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>Définir explicitement toutes les propriétés de la politique au niveau de la table

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>Définir explicitement toutes les propriétés de la stratégie au niveau de la base de données

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>Définir la politique d’enhardis par *défaut* au niveau de la base de données

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>Modification d’une propriété unique de la politique au niveau de la base de données 

Gardez toutes les autres propriétés telles qu’elle est.

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>Modification d’une propriété unique de la politique au niveau de la table

Conserver toutes les autres propriétés telles qu’est

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>supprimer la politique

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

La commande supprime la stratégie d’enhardisement actuelle pour l’entité donnée.