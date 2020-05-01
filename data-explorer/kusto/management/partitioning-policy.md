---
title: Gestion de la stratégie de partitionnement des données-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la gestion des stratégies de partitionnement des données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: 1ad9b359422b51084f1be1c64d27d656313d9296
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616318"
---
# <a name="data-partitioning-policy-management"></a>Gestion des stratégies de partitionnement des données

La stratégie de partitionnement des données est détaillée [ici](../management/partitioningpolicy.md).

## <a name="show-policy"></a>afficher la stratégie

```kusto
.show table [table_name] policy partitioning
```

La `.show` commande affiche la stratégie de partitionnement appliquée à la table.

### <a name="output"></a>Output

|Nom de stratégie | Nom de l’entité | Stratégie | Entités enfants | Type d'entité
|---|---|---|---|---
|DataPartitioning | Nom de la table | Sérialisation JSON de l’objet de stratégie | null | Table de charge de travail

## <a name="alter-and-alter-merge-policy"></a>stratégie ALTER et ALTER-Merge

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

La `.alter` commande permet de modifier la stratégie de partitionnement appliquée à la table.

La commande requiert des autorisations [DatabaseAdmin](access-control/role-based-authorization.md) .

La prise en compte des modifications apportées à la stratégie peut prendre jusqu’à 1 heure.

### <a name="examples"></a>Exemples

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>Définition explicite de toutes les propriétés de la stratégie au niveau de la table

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
    '{'
      '"ColumnName": "my_string_column",'
      '"Kind": "Hash",'
      '"Properties": {'
        '"Function": "XxHash64",'
        '"MaxPartitionCount": 256,'
      '}'
    '},'
    '{'
      '"ColumnName": "my_datetime_column",'
      '"Kind": "UniformRange",'
      '"Properties": {'
        '"Reference": "1970-01-01T00:00:00",'
        '"RangeSize": "1.00:00:00"'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>Définition explicite d’une propriété spécifique de la stratégie au niveau de la table

Pour définir la `EffectiveDateTime` valeur de la stratégie sur une autre valeur, utilisez la commande suivante :

```kusto
.alter-merge table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>supprimer la stratégie

```kusto
.delete table [table_name] policy partitioning
```

La `.delete` commande supprime la stratégie de partitionnement de la table donnée.
