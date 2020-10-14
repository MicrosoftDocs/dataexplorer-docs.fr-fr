---
title: La stratégie Kusto IngestionBatching optimise le traitement par lot-Azure Explorateur de données
description: Cet article décrit la stratégie IngestionBatching dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 27c92b9081844df0e8f8d4dc207ba5d2a8a2e2f3
ms.sourcegitcommit: a10e7c6ba96bdb94d95ef23f5d1506eb8fda0041
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058663"
---
# <a name="ingestionbatching-policy"></a>Stratégie IngestionBatching

## <a name="overview"></a>Vue d’ensemble

Durant le processus d’ingestion, Kusto tente d’optimiser le débit en regroupant par lot des segments de données d’entrée de petite taille en attente d’ingestion.
Ce type de traitement par lot réduit les ressources consommées par le processus d’ingestion, et ne nécessite pas de ressources de publication pour optimiser les petits partitions de données générés par l’ingestion non groupée par lot.

Toutefois, il existe un inconvénient à effectuer le traitement par lot avant l’ingestion, qui est l’introduction d’un délai forcé, afin que l’heure de bout en bout de la demande d’ingestion de données jusqu’à ce qu’il soit prêt pour la requête soit plus importante.

Pour permettre le contrôle de ce compromis, il est possible d’utiliser la `IngestionBatching` stratégie.
Cette stratégie est appliquée uniquement à la réception en file d’attente, et indique le délai maximal imposé à autoriser lors du traitement par lot de petits objets BLOB.

## <a name="details"></a>Détails

Comme expliqué ci-dessus, il existe une taille optimale de données à réceptionner en bloc.
Actuellement, la taille est d’environ 1 Go de données non compressées. L’ingestion qui s’effectue dans les objets BLOB qui contiennent bien moins de données que la taille optimale est non optimale et, par conséquent, dans une ingestion en attente, Kusto regroupera ces petits objets BLOB. Le traitement par lot est effectué jusqu’à ce que la première condition devienne vraie :

1. La taille totale des données traitées par lot atteint la taille optimale, ou
2. Le délai maximal, la taille totale ou le nombre d’objets BLOB autorisés par la `IngestionBatching` stratégie est atteint.

La `IngestionBatching` stratégie peut être définie sur des bases de données ou des tables. Par défaut, si aucune stratégie n’est définie, Kusto utilise une valeur par défaut de **5 minutes** comme délai maximal, **1000** éléments, taille totale de **1g** pour le traitement par lot.

> [!WARNING]
> L’impact de la définition de cette stratégie sur une valeur très faible est une augmentation du COGS du cluster et une baisse des performances. En outre, dans la limite, la réduction de cette valeur peut en effet entraîner une latence d’ingestion de bout en bout **accrue** , en raison de la surcharge liée à la gestion de plusieurs processus d’ingestion en parallèle.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Référence des commandes de la stratégie IngestionBatching](../management/batching-policy.md)
* [Meilleures pratiques d’ingestion-optimisation pour le débit](../api/netfx/kusto-ingest-best-practices.md#optimizing-for-throughput)
