---
title: Gestion des politiques de partitionnement des données - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des politiques de partitionnement des données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: 0e1ff783195f26adf7f98e511ca155f43609098c
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663965"
---
# <a name="data-partitioning-policy-management"></a>Gestion des politiques de partage des données

La politique de partage des données est détaillée [ici](../management/partitioningpolicy.md).

## <a name="show-policy"></a>montrer la politique

```kusto
.show table [table_name] policy partitioning
```

La `.show` commande affiche la politique de partitionnement qui est appliquée sur la table.

### <a name="output"></a>Output

|Nom de stratégie | Nom de l’entité | Stratégie | Entités enfants | Type d'entité
|---|---|---|---|---
|DonnéesPartition | Nom de la table | Sérialisation JSON de l’objet politique | null | Table de charge de travail

## <a name="alter-and-alter-merge-policy"></a>modifier et modifier la politique

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

La `.alter` commande permet de modifier la politique de partitionnement qui est appliquée sur la table.

La commande nécessite des autorisations [DatabaseAdmin.](access-control/role-based-authorization.md)

Les modifications apportées à la politique pourraient prendre jusqu’à 1 heure pour entrer en vigueur.

### <a name="examples"></a>Exemples

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>Définir explicitement toutes les propriétés de la politique au niveau de la table

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

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>Établir explicitement une propriété spécifique de la politique au niveau de la table

Pour définir `EffectiveDateTime` la stratégie à une valeur différente, utilisez la commande suivante :

```kusto
.alter table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>supprimer la politique

```kusto
.delete table [table_name] policy partitioning
```

La `.delete` commande supprime la politique de partitionnement de la table donnée.
