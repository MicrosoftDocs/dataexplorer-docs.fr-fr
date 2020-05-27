---
title: Gestion de la stratégie de fusion-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la gestion de la stratégie de fusion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9ef6f2cd2359e35e90c3903738adf82728e9da40
ms.sourcegitcommit: a562ce255ac706ca1ca77d272a97b5975235729d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867067"
---
# <a name="merge-policy-management"></a>Gestion de la stratégie de fusion

## <a name="show-policy"></a>afficher la stratégie

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

Affiche la stratégie de fusion actuelle pour la base de données ou la table.
Affiche toutes les stratégies du type d’entité donné (base de données ou table) si le nom donné est « * ».

### <a name="output"></a>Output

|Nom de la stratégie | Nom de l’entité | Stratégie | Entités enfants | Type d’entité
|---|---|---|---|---
|ExtentsMergePolicy | Nom de la base de données/table | chaîne au format JSON qui représente la stratégie. | Liste de tables (pour une base de données)|Table de &#124; de base de données

## <a name="alter-policy"></a>modifier la stratégie

### <a name="examples"></a>Exemples

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. définition explicite de toutes les propriétés de la stratégie, au niveau de la table :

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. définition explicite de toutes les propriétés de la stratégie, au niveau de la base de données :

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. définition de la stratégie de fusion *par défaut* au niveau de la base de données :

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. modification d’une propriété unique de la stratégie au niveau de la base de données, en conservant toutes les autres propriétés en l’or :

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. modification d’une propriété unique de la stratégie au niveau de la table, en conservant toutes les autres propriétés en l’or :

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

Tous les éléments ci-dessus retournent la stratégie de fusion des extensions mises à jour pour l’entité (base de données ou table spécifiée comme nom qualifié) comme sortie.

La prise en compte des modifications apportées à la stratégie peut prendre jusqu’à 1 heure.

## <a name="delete-policy-of-merge"></a>supprimer la stratégie de fusion

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

La commande supprime la stratégie de fusion actuelle pour l’entité donnée.
