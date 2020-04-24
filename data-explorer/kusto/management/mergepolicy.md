---
title: Politiques de fusion d’étendues - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique de fusion d’Étendues dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 771af22f07a770b0da1196871e9132393524aab9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520721"
---
# <a name="extents-merge-policy"></a>Politiques de fusion d’étendues
La politique de fusion définit si et comment [Les étendues (Data Shards)](../management/extents-overview.md) dans le cluster Kusto devraient être fusionnées.

Il y a 2 saveurs pour les opérations de fusion : `Merge` (qui reconstruit les index), et `Rebuild` (qui réingére complètement les données).

Les deux types d’opération se traduisent par une seule mesure qui remplace les étendues de source.

Par défaut, les opérations de reconstruction sont préférées, et seulement s’il reste des étendues qui ne correspondaient pas aux critères de reconstruction, ils sont tentés d’être fusionnés.  

*Remarques :*
- L’utilisation *d’étiquettes différentes* `drop-by` entraînera la non fusion de ces étendues, même si une politique de fusion a été définie (voir [Le marquage de l’étendue).](../management/extents-overview.md#extent-tagging)
- Les étendues dont l’union des étiquettes dépasse la longueur des caractères 1M ne seront pas fusionnées.
- La politique de [Sharding](./shardingpolicy.md) de la base de données et de la table a également un certain effet sur la façon dont les étendues sont fusionnées.

La politique de fusion contient les propriétés suivantes :

- **RowCountUpperBoundForMerge**:
    - La valeur par défaut est 0.
    - Nombre maximal de rangées autorisées de l’étendue fusionnée.
    - S’applique aux opérations de fusion, pas à la reconstruction.  
- **MaxExtentsToMerge**:
    - Par défaut à 100.
    - Nombre maximal autorisé d’étendues à fusionner en une seule opération.
    - S’applique aux opérations de fusion.
- **LoopPeriod**:
    - Défauts à 01:00:00 (1 heure).
    - Le temps maximum d’attente entre le démarrage de 2 itérations consécutives d’opérations de fusion/reconstruction (effectuée par le service de gestion des données).
    - S’applique à la fois aux opérations de fusion et de reconstruction.
- **AllowRebuild**:
    - Par défaut à «vrai».
    - Définit si `Rebuild` les opérations sont activées (dans `Merge` ce cas, elles sont préférées aux opérations).
- **AllowMerge**:
    - Par défaut à «vrai».
    - Définit si `Merge` les opérations sont activées (dans ce `Rebuild` cas, elles sont moins préférées que les opérations).
- **MaxRangeInHours**:
    - Par défaut 8.
    - Différence maximale autorisée (en heures) entre les 2 temps de création des différentes étendues, de sorte qu’ils peuvent encore être fusionnés.
    - Les timestamps sont ceux de la création d’étendue, et ne se rapportent pas aux données réelles contenues dans les étendues.
    - S’applique à la fois aux opérations de fusion et de reconstruction.
    - Une meilleure pratique est que cette valeur soit corrélée avec la politique de [rétention](./retentionpolicy.md)de la base de données / table *SoftDeletePeriod*, ou la [politique cache](./cachepolicy.md) *DataHotSpan* (le plus bas des deux), de sorte qu’il est entre 2-3% de ce dernier.

**`MaxRangeInHours`Exemples:**
|min (SoftDeletePeriod (Politique de rétention), DataHotSpan (Politique cache))|Max Range In Hours (Politique de fusion)
|---|---
|7 jours (168 heures)| 4
|14 jours (336 heures)| 8
|30 jours (720 heures)| 18
|60 jours (1 440 heures)| 36
|90 jours (2 160 heures)| 60
|180 jours (4 320 heures)| 120
|365 jours (8 760 heures)| 250

> [!WARNING]
> Il est rarement recommandé de modifier une politique de fusion d’étendues sans consulter d’abord l’équipe Kusto.

Lorsqu’une base de données est créée, elle est définie avec la politique de fusion par défaut (une politique avec les valeurs par défaut mentionnées ci-dessus), qui est, par défaut, héritée par défaut de toutes les tables créées dans la base de données (sauf si leurs politiques sont explicitement remplacées au niveau de la table).

Les commandes de contrôle qui permettent de gérer les politiques de fusion pour les bases de données / tables peuvent être trouvées [ici](../management/merge-policy.md).