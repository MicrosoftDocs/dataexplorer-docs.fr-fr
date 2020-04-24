---
title: Politique d’ingestionBatching - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique d’IngestionBatching dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: fa3a4cfd512e3e37ba6b6abf8f8316d6ff12fdec
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522234"
---
# <a name="ingestionbatching-policy"></a>Politique d’ingestionBatching

## <a name="overview"></a>Vue d’ensemble

Pendant le processus d’ingestion Kusto tente d’optimiser le débit en lotant de petits morceaux de données d’entrée ensemble pendant qu’ils attendent l’ingestion.
Ce type de lotage réduit les ressources consommées par le processus d’ingestion, ainsi que ne nécessite pas de ressources post-ingestion pour optimiser les petits éclats de données produits par l’ingestion non-batched.

Il y a un inconvénient, cependant, à effectuer le lotage avant l’ingestion, qui est l’introduction d’un retard forcé, de sorte que le temps de bout en bout de demander l’ingestion de données jusqu’à ce qu’il soit prêt pour la requête est plus grande.

Pour permettre le contrôle de ce compromis, on peut utiliser la `IngestionBatching` politique.
Cette politique est appliquée uniquement à l’ingestion en file d’attente, et fournit le délai maximal forcé pour permettre lors de la mise en lots de petits blobs ensemble.

## <a name="details"></a>Détails

Comme expliqué ci-dessus, il y a une taille optimale de données à ingérer en vrac.
Actuellement, cette taille est d’environ 1 Go de données non compressées. Ingestion qui se fait en blobs qui contiennent beaucoup moins de données que la taille optimale n’est pas optimale, et donc dans l’ingestion en file d’attente Kusto va loter ces petits blobs ensemble. Le lotage est fait jusqu’à ce que la première condition devienne vraie :

1. La taille totale des données par lots atteint la taille optimale, ou
2. Le délai maximal, la taille totale ou le `IngestionBatching` nombre de blobs autorisés par la police sont atteints

La `IngestionBatching` stratégie peut être définie sur les bases de données ou les tableaux. Par défaut, si aucune stratégie n’est définie, Kusto utilisera une valeur par défaut de **5 minutes** comme délai maximum, **1000** éléments, taille totale de **1G** pour le lotage.

> [!WARNING]
> Il est recommandé que les clients qui souhaitent définir cette politique communiquent d’abord avec l’équipe des opérations Kusto. L’impact de l’établissement de cette politique à une valeur très faible est une augmentation du COGS du cluster et une performance réduite. En outre, dans la limite, la réduction de cette valeur pourrait en fait entraîner une latence efficace d’ingestion de bout en bout, en raison des frais généraux de gestion de multiples processus d’ingestion en parallèle. **increased**

## <a name="additional-resources"></a>Ressources supplémentaires

* [IngestionBatching politique commande référence](../management/batching-policy.md)
* [Pratiques exemplaires d’ingestion - optimisation du débit](../api/netfx/kusto-ingest-best-practices.md#optimizing-for-throughput)