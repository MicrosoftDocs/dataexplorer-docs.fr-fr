---
title: Stratégie de fusion des extensions-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les étendues de la stratégie de fusion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: f0398fbc19842b3cce7fe69c8cb61258d0aeaaa6
ms.sourcegitcommit: a562ce255ac706ca1ca77d272a97b5975235729d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867033"
---
# <a name="extents-merge-policy"></a>Stratégie de fusion des étendues
La stratégie de fusion définit si et comment les [étendues (données partitions)](../management/extents-overview.md) du cluster Kusto doivent être fusionnées.

Il existe deux types d’opérations de fusion : `Merge` (qui reconstruit les index) et `Rebuild` (qui reconstruit complètement les données).

Les deux types d’opérations entraînent une seule extension, qui remplace les extensions sources.

Par défaut, les opérations de régénération sont préférées et, uniquement s’il existe des extensions restantes qui ne correspondent pas aux critères de reconstruction, elles sont tentées d’être fusionnées.  

*Remarques :*
- Les extensions de balisage utilisant *different* `drop-by` des balises différentes entraînent la non-fusion de ces étendues, même si une stratégie de fusion a été définie (Voir l' [étiquetage des étendues](../management/extents-overview.md#extent-tagging)).
- Les extensions dont l’Union de balises dépasse la longueur de 1 million de caractères ne sont pas fusionnées ensemble.
- La [stratégie partitionnement](./shardingpolicy.md) de la base de données ou de la table a également un effet sur la façon dont les extensions sont fusionnées ensemble.

La stratégie de fusion contient les propriétés suivantes :

- **RowCountUpperBoundForMerge**:
    - Configuration par défaut :
      - 0 (illimité) pour les stratégies qui ont été définies avant le 2020 juin.
      - 16 millions pour les stratégies qui ont été définies à partir du 2020 juin.
    - Nombre maximal de lignes autorisées de l’étendue fusionnée.
    - S’applique aux opérations de fusion, et non à la régénération.  
- **OriginalSizeMBUpperBoundForMerge**:
    - La valeur par défaut est 0 (illimité).
    - Taille maximale autorisée (en Mo) de l’étendue fusionnée.
    - S’applique aux opérations de fusion, et non à la régénération.  
- **MaxExtentsToMerge**:
    - La valeur par défaut est 100.
    - Nombre maximal d’étendues autorisées à fusionner en une seule opération.
    - S’applique aux opérations de fusion.
- **LoopPeriod**:
    - La valeur par défaut est 01:00:00 (1 heure).
    - Durée d’attente maximale entre le démarrage de deux itérations consécutives d’opérations de fusion et de régénération (effectuées par le service Gestion des données).
    - S’applique aux opérations de fusion et de régénération.
- **AllowRebuild**:
    - La valeur par défaut est « true ».
    - Définit si les `Rebuild` opérations sont activées (dans ce cas, elles sont préférées aux `Merge` opérations).
- **AllowMerge**:
    - La valeur par défaut est « true ».
    - Définit si les `Merge` opérations sont activées (dans ce cas, elles sont moins préférées que les `Rebuild` opérations).
- **MaxRangeInHours**:
    - La valeur par défaut est 8.
    - Différence maximale autorisée (en heures) entre deux durées de création d’étendues différentes, afin qu’elles puissent toujours être fusionnées.
    - Les horodateurs sont ceux de la création d’étendues et ne sont pas liés aux données réelles contenues dans les étendues.
    - S’applique aux opérations de fusion et de régénération.
    - Une meilleure pratique est que cette valeur soit corrélée avec le *SoftDeletePeriod*de la [stratégie de rétention](./retentionpolicy.md)de la base de données/de la table, ou le *DataHotSpan* de la [stratégie de cache](./cachepolicy.md)(le plus bas des deux), afin qu’elle soit comprise entre 2-3% de la dernière.

**`MaxRangeInHours`illustre**

|min (SoftDeletePeriod (stratégie de rétention), DataHotSpan (stratégie de cache))|Plage maximale en heures (stratégie de fusion)|
|--------------------------------------------------------------------|---------------------------------|
|7 jours (168 heures)                                                  | 4                               |
|14 jours (336 heures)                                                 | 8                               |
|30 jours (720 heures)                                                 | 18                              |
|60 jours (1 440 heures)                                               | 36                              |
|90 jours (2 160 heures)                                               | 60                              |
|180 jours (4 320 heures)                                              | 120                             |
|365 jours (8 760 heures)                                              | 250                             |

> [!WARNING]
> Consultez l’équipe de Explorateur de données Azure avant de modifier une stratégie de fusion d’étendues.

Lorsqu’une base de données est créée, elle est définie avec la stratégie de fusion par défaut (une stratégie avec les valeurs par défaut mentionnées ci-dessus), qui est, par défaut, héritée par toutes les tables créées dans la base de données (sauf si leurs stratégies sont remplacées explicitement au niveau de la table).

Les commandes de contrôle qui permettent de gérer les stratégies de fusion pour les bases de données/tables sont disponibles [ici](../management/merge-policy.md).
