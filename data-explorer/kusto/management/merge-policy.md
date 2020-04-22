---
title: Gestion des politiques de fusion - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des politiques de fusion dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4093c9c09e4e268bc38cabdc6da27f0ac2ee17ab
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81664021"
---
# <a name="merge-policy-management"></a>Fusion de la gestion des politiques

## <a name="show-policy"></a>montrer la politique

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

Affiche la stratégie de fusion actuelle pour la base de données ou la table.
Affiche toutes les stratégies du type d’entité donnée (base de données ou tableau) si le nom donné est ''.

### <a name="output"></a>Output

|Nom de la stratégie | Nom de l’entité | Stratégie | Entités enfants | Type d’entité
|---|---|---|---|---
|ÉtenduesMergePolicy | Base de données / Nom de table | une chaîne formatée par JSON qui représente la politique | Une liste de tables (pour une base de données)|Table de &#124; de base de données

## <a name="alter-policy"></a>modifier la politique

### <a name="examples"></a>Exemples

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. Définir explicitement toutes les propriétés de la politique au niveau de la table :

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000, '
    '"RowCountUpperBoundForMerge": 0, '
    '"MaxExtentsToMerge": 100, '
    '"LoopPeriod": "01:00:00", '
    '"MaxRangeInHours": 8, '
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. Définir explicitement toutes les propriétés de la stratégie au niveau de la base de données :

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. Définir la politique de fusion *par défaut* au niveau de la base de données :

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. Modification d’une propriété unique de la politique au niveau de la base de données, en conservant toutes les autres propriétés telles qu’elle est :

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. Modification d’une propriété unique de la police au niveau de la table, en conservant toutes les autres propriétés telles que :

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

Tous les rendements ci-dessus la politique de fusion des étendues mises à jour pour l’entité (base de données ou tableau spécifié comme nom qualifié) comme leur sortie.

Les modifications apportées à la politique pourraient prendre jusqu’à 1 heure pour entrer en vigueur.

## <a name="delete-policy-of-merge"></a>supprimer la politique de fusion

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

La commande supprime la stratégie de fusion actuelle pour l’entité donnée.
