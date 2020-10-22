---
title: Azure Data Explorer Kusto EngineV3 (préversion)
description: En savoir plus sur Azure Data Explorer (Kusto) EngineV3
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: e018e8ae6b25437a8665a5b5eb90fc2bac4ce9b9
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003269"
---
# <a name="enginev3---preview"></a>EngineV3 - préversion

Kusto EngineV3 est le moteur de stockage et de requête nouvelle génération d’Azure Data Explorer. Il est conçu pour fournir des performances inégalées pour l’ingestion et l’interrogation des données de télémétrie, des journaux et des séries chronologiques.

EngineV3 comprend un nouveau format de stockage optimisé et des index. EngineV3 utilise des optimisations des requêtes basées sur des statistiques avancées des données pour créer un plan de requête optimal et l’exécution des requêtes compilées juste-à-temps. EngineV3 a également amélioré le cache de disque, ce qui a pour résultat une amélioration très importante des performances des requêtes par rapport au moteur actuel (EngineV2). EngineV3 pose les bases de la prochaine vague d’innovations du service Azure Data Explorer.

Le cluster Azure Data Explorer s’exécutant en mode EngineV3 est entièrement compatible avec EngineV2, de sorte qu’une migration des données n’est pas nécessaire.

> [!IMPORTANT]
> EngineV3 sera disponible selon les étapes suivantes :
>
> 1. Préversion publique (état actuel) : Les utilisateurs peuvent créer des clusters en mode EngineV3. Pendant la période de préversion publique, les clusters ne sont pas soumis à un contrat SLA et ne sont pas facturés pour le balisage Azure Data Explorer. Les coûts de l’infrastructure sont facturés comme d’habitude.
> 1. Disponibilité générale : Tous les nouveaux clusters sont créés en mode EngineV3 par défaut. Le contrat SLA s’applique à tous les clusters de production EngineV3 et EngineV2.
> 1. Après la disponibilité générale : Les charges de travail existantes qui s’exécutent sur EngineV2 sont migrées vers EngineV3. Les frais du balisage Azure Data Explorer seront à nouveau facturés.

## <a name="how-enginev3-works"></a>Fonctionnement d’EngineV3

EngineV3 est un moteur de stockage orienté colonnes supplémentaire qui s’exécute en parallèle avec le magasin de colonnes (EngineV2) et le magasin de lignes (utilisé pour l’ingestion en streaming) existants. Les tables peuvent incorporer des données provenant des trois magasins à la fois, et cette « fédération » de données est transparente pour l’utilisateur.

:::image type="content" source="media\engine-v3\engine-v3-architecture.png" alt-text="Représentation schématique de l’architecture Azure Data Explorer/Kusto EngineV3":::

Toutes les données ingérées dans les tables sont disposées en partitions, qui sont des tranches horizontales de la table. Chaque partition contient généralement quelques millions d’enregistrements, et elle est encodée et indexée indépendamment des autres partitions. Cette fonctionnalité permet au moteur d’avoir un débit d’ingestion progressant de façon linéaire.

Les partitions sont réparties uniformément entre les nœuds de cluster, où elles sont mises en cache à la fois sur le SSD local et en mémoire. Le planificateur de requête et le moteur de requête préparent et exécutent une requête parallèle fortement distribuée qui tire parti de cette distribution et de la mise en cache des partitions.

EngineV3 s’occupe principalement de l’optimisation de cette « partie inférieure » de la requête distribuée.

## <a name="performance"></a>Performances

Les performances améliorées et la rapidité accrue des requêtes proviennent des deux modifications majeures apportées au moteur :

* Nouveau format amélioré du stockage des partitions.
* Reconception du moteur de requête de partitions de bas niveau.

L’impact sur les performances d’EngineV3 dépend du jeu de données, des modèles de requête, des accès concurrentiels et des références SKU des machines virtuelles utilisées. Dans les tests de performances, un jeu de données de 100 To a été utilisé, et différents scénarios impliquant de l’analytique sur des données structurées, non structurées et semi-structurées ont été explorés. Avec le même niveau d’accès concurrentiel et l’utilisation de la même configuration matérielle, l’amélioration des performances était en moyenne d’un facteur 8. L’amélioration réelle des performances varie en fonction de la requête et du jeu de données.

## <a name="create-an-enginev3-cluster"></a>Créer un cluster EngineV3

Pour [créer un cluster](create-cluster-database-portal.md) avec EngineV3, sous l’onglet **Général** de l’écran de création de cluster, cochez la case **Utiliser EngineV3 (préversion)**  :

:::image type="content" source="media/engine-v3/create-new-cluster-v3.png" alt-text="Représentation schématique de l’architecture Azure Data Explorer/Kusto EngineV3":::

## <a name="next-steps"></a>Étapes suivantes

[Ingérer des données avec Azure Data Explorer](ingest-data-overview.md)
