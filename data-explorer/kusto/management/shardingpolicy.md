---
title: Stratégie de partitionnement des données-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la stratégie de partitionnement de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 70d4013c8524fa88249de14a0a67cc8a85e73b3f
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337565"
---
# <a name="data-sharding-policy"></a>Stratégie de partitionnement de données

La stratégie partitionnement définit si et comment les [étendues (données partitions)](../management/extents-overview.md) du cluster Explorateur de données Azure doivent être sealed.

> [!NOTE]
> La stratégie s’applique à toutes les opérations qui créent de nouvelles étendues, telles que les commandes pour l’ingestion des [données](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands)et les [commandes. Merge et. Rebuild](./merge-extents.md)

La stratégie de partitionnement de données contient les propriétés suivantes :

- **MaxRowCount**:
    - Nombre maximal de lignes pour une extension créée par une opération d’ingestion ou de régénération.
    - La valeur par défaut est 750 000.
    - **Non applicable** pour les [opérations de fusion](mergepolicy.md).
        - Si vous devez limiter le nombre de lignes dans les étendues créées par les opérations de fusion, ajustez la `RowCountUpperBoundForMerge` propriété dans la [stratégie de fusion d’étendues](mergepolicy.md)de l’entité.
- **MaxExtentSizeInMb**:
    - Taille maximale autorisée de données compressées (en mégaoctets) pour une extension créée par une opération de fusion.
    - En vigueur **uniquement pour les opérations de [fusion](mergepolicy.md) **.
    - La valeur par défaut est 1 024 (1 Go).

- **MaxOriginalSizeInMb**:
    - Taille maximale autorisée des données d’origine (en mégaoctets) pour une extension créée par une opération de reconstruction.
    - En vigueur **uniquement pour les opérations de [reconstruction](mergepolicy.md) **.
    - La valeur par défaut est 2 048 (2 Go).

> [!WARNING]
> Consultez l’équipe de Explorateur de données Azure avant de modifier une stratégie de partitionnement de données.

Lorsqu’une base de données est créée, elle contient la stratégie de partitionnement de données par défaut. Cette stratégie est héritée par toutes les tables créées dans la base de données (sauf si la stratégie est explicitement remplacée au niveau de la table).

Utilisez les [commandes de contrôle de stratégie partitionnement](../management/sharding-policy.md)) pour gérer les stratégies de partitionnement de données pour les bases de données et les tables.
