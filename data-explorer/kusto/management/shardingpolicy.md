---
title: Politique d’enhardisage des données - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique d’éclat de données dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6ed9cf3a1667fb57271a44dcfe7c6dd5318c4010
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520024"
---
# <a name="data-sharding-policy"></a>Politique d’hardissant des données

La stratégie d’enhardis définit si et comment [les étendues (éclats de données)](../management/extents-overview.md) dans le cluster Azure Data Explorer doivent être scellées.

> [!NOTE]
> La politique s’applique à toutes les opérations qui créent de nouvelles étendues, telles que les commandes pour [l’ingestion de données,](../management/data-ingestion/index.md)et [.merge et .reconstruire les commandes](../management/extents-commands.md#merge-extents)

La politique d’enhardisage des données contient les propriétés suivantes :

- **MaxRowCount**:
    - Nombre maximum de rangées pour une mesure créée par une opération d’ingestion ou de reconstruction.
    - Par défaut à 750 000.
    - **Pas en vigueur** pour [les opérations de fusion](mergepolicy.md).
        - Si vous devez limiter le nombre de lignes dans les `RowCountUpperBoundForMerge` étendues créées par les opérations de fusion, ajustez la propriété dans la politique de fusion des [étendues](mergepolicy.md)de l’entité .
- **MaxExtentSizeInMb**:
    - Taille maximale des données compressées autorisées (en mégaoctets) dans une mesure créée par une opération de fusion.
    - En effet **seulement pour les opérations de [fusion](mergepolicy.md) **.
    - Par défaut à 1 024 (1 Go).

- **MaxOriginalSizeInMb**:
    - Taille maximale des données d’origine autorisées (en mégaoctets) dans une mesure créée par une opération de reconstruction.
    - En effet **seulement pour les opérations de [reconstruction](mergepolicy.md) **.
    - Par défaut à 2 048 (2 Go).

> [!WARNING]
> Consultez l’équipe Azure Data Explorer avant de modifier une politique d’enhardisage des données.

Lorsqu’une base de données est créée, elle contient la stratégie d’enhardisage des données par défaut. Cette politique est héritée de toutes les tables créées dans la base de données (à moins que la politique ne soit explicitement dépassée au niveau de la table).

Utilisez les [commandes de contrôle de stratégie enhardis](../management/sharding-policy.md)) pour gérer les stratégies d’enhardisage des données pour les bases de données et les tableaux.
 