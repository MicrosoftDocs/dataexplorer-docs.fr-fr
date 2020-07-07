---
title: Gestion de la stratégie de partitionnement-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la gestion de la stratégie partitionnement dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb4ff4ade5e3fb0e2f01de0adc74aecd27381a3d
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967466"
---
# <a name="sharding-policy-command"></a>Commande de stratégie de sharding

## <a name="show-policy"></a>afficher la stratégie

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`la stratégie affiche la stratégie partitionnement pour la base de données ou la table. Elle affiche toutes les stratégies du type d’entité donné (base de données ou table) si le nom donné est' * '.

### <a name="output"></a>Sortie

|Nom de stratégie | Nom de l’entité | Stratégie | Entités enfants | Type d'entité
|---|---|---|---|---
|ExtentsShardingPolicy | nom de la base de données/table | chaîne de format JSON qui représente la stratégie. | Liste des tables (pour une base de données)|base de données/table

## <a name="alter-policy"></a>modifier la stratégie

### <a name="examples"></a>Exemples

Les exemples suivants retournent la stratégie partitionnement des extensions mises à jour pour l’entité, avec la base de données ou la table spécifiée comme nom qualifié, comme sortie.

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>Définition explicite de toutes les propriétés de la stratégie au niveau de la table

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>Définition explicite de toutes les propriétés de la stratégie au niveau de la base de données

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>Définition de la stratégie partitionnement *par défaut* au niveau de la base de données

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>Modification d’une propriété unique de la stratégie au niveau de la base de données 

Conservez toutes les autres propriétés en l’or.

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>Modification d’une propriété unique de la stratégie au niveau de la table

Conserver toutes les autres propriétés en l’or

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>supprimer la stratégie

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

La commande supprime la stratégie partitionnement actuelle pour l’entité donnée.