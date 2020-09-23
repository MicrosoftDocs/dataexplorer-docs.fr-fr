---
title: Stratégie de fusion des extensions-Azure Explorateur de données
description: Cet article décrit les étendues de la stratégie de fusion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 87980046e6f0ebbbdd17a9037aa1206779d2a61e
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057034"
---
# <a name="merge-policy"></a>Stratégie de fusion

La stratégie de fusion définit si et comment les [étendues (données partitions)](../management/extents-overview.md) du cluster Kusto doivent être fusionnées.

Il existe deux types d’opérations de fusion : `Merge` , qui reconstruit des index et `Rebuild` , qui reconstruit complètement les données.

Les deux types d’opérations entraînent une seule extension qui remplace les extensions sources.

Par défaut, les opérations de régénération sont préférées. S’il existe des étendues qui ne correspondent pas aux critères de reconstruction, une tentative de fusion est effectuée.  

> [!NOTE]
> * Les extensions de balisage utilisant *different* `drop-by` des balises différentes entraînent la non-fusion de telles étendues, même si une stratégie de fusion a été définie. Pour plus d’informations, consultez [étiquetage des étendues](../management/extents-overview.md#extent-tagging).
> * Les extensions dont l’Union de balises dépasse la longueur de 1 million de caractères ne sont pas fusionnées.
> * La [stratégie partitionnement](./shardingpolicy.md) de la base de données ou de la table a également un effet sur la façon dont les extensions sont fusionnées.

## <a name="merge-policy-properties"></a>Propriétés de la stratégie de fusion

La stratégie de fusion contient les propriétés suivantes :

* **RowCountUpperBoundForMerge**:
    * Configuration par défaut :
      * 0 (illimité) pour les stratégies qui ont été définies avant le 2020 juin.
      * 16 millions pour les stratégies qui ont été définies à partir du 2020 juin.
    * Nombre maximal de lignes autorisées de l’étendue fusionnée.
    * S’applique aux opérations de fusion, et non à la régénération.  
* **OriginalSizeMBUpperBoundForMerge**:
    * La valeur par défaut est 0 (illimité).
    * Taille maximale autorisée (en Mo) de l’étendue fusionnée.
    * S’applique aux opérations de fusion, et non à la régénération.  
* **MaxExtentsToMerge**:
    * La valeur par défaut est 100.
    * Nombre maximal d’étendues autorisées à fusionner en une seule opération.
    * S’applique aux opérations de fusion.
* **LoopPeriod**:
    * La valeur par défaut est 01:00:00 (1 heure).
    * Délai d’attente maximal entre le démarrage de deux itérations consécutives d’opérations de fusion ou de régénération par le service Gestion des données.
    * S’applique aux opérations de fusion et de régénération.
* **AllowRebuild**:
    * La valeur par défaut est « true ».
    * Définit si les `Rebuild` opérations sont activées (dans ce cas, elles sont préférées aux `Merge` opérations).
* **AllowMerge**:
    * La valeur par défaut est « true ».
    * Définit si les `Merge` opérations sont activées, auquel cas elles sont moins préférées que les `Rebuild` opérations.
* **MaxRangeInHours**:
    * La valeur par défaut est 8.
        * La valeur par défaut est de 14 jours dans les [vues matérialisées](materialized-views/materialized-view-overview.md), sauf si la récupération est désactivée dans la [stratégie de rétention](retentionpolicy.md)effective des vues matérialisées.
    * Différence maximale autorisée, en heures, entre deux durées de création d’étendues différentes, afin qu’elles puissent toujours être fusionnées.
    * Les horodateurs sont de la création d’extension et ne sont pas liés aux données réelles contenues dans les étendues.
    * S’applique aux opérations de fusion et de régénération.
    * Cette valeur doit être définie en fonction des valeurs de [stratégie de rétention](./retentionpolicy.md) *SoftDeletePeriod*ou de la [stratégie de cache](./cachepolicy.md) *DataHotSpan* . Prenez la valeur inférieure de *SoftDeletePeriod* et *DataHotSpan*. Définissez la valeur *MaxRangeInHours* sur une valeur comprise entre 2-3%. Consultez les [exemples](#maxrangeinhours-examples) .

## <a name="maxrangeinhours-examples"></a>Exemples MaxRangeInHours

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

Lorsqu’une base de données est créée, elle est définie avec les valeurs de stratégie de fusion par défaut mentionnées ci-dessus. Par défaut, la stratégie est héritée par toutes les tables créées dans la base de données, sauf si leurs stratégies sont remplacées explicitement au niveau de la table.

Pour plus d’informations, consultez [commandes de contrôle qui vous permettent de gérer les stratégies de fusion pour les bases de données ou les tables](../management/merge-policy.md).
