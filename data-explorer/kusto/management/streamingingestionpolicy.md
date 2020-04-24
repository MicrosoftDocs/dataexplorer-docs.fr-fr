---
title: Politique d’ingestion en continu - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique d’ingestion en continu dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: ef4199e0cd8c1e14839e5508a9c6a0d793de0cc0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519599"
---
# <a name="streaming-ingestion-policy"></a>Stratégie d’ingestion de streaming

## <a name="streaming-ingestion-target-scenario"></a>Scénario cible d’ingestion en continu

L’ingestion de streaming cible les scénarios qui nécessitent une faible latence avec une durée d’ingestion de moins de 10 secondes pour des volumes de données variés. Il est utilisé pour optimiser le traitement opérationnel de nombreux tableaux, dans une ou plusieurs bases de données, où le flux de données dans chaque table est relativement faible (peu d’enregistrements par seconde), mais le volume global d’ingestion de données est élevé (des milliers d’enregistrements par seconde).

Utilisez l’ingestion classique (en bloc) à la place de l’ingestion de streaming lorsque la quantité de données dépasse 1 Mo par seconde par table. 

* Pour apprendre à implémenter cette fonctionnalité, voir [l’ingestion en streaming](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming).
* Pour obtenir de l’information sur les commandes de contrôle d’ingestion en continu, voir [les commandes de contrôle sont utilisées pour gérer la politique d’ingestion en continu](../management/streamingingestion-policy.md)

## <a name="streaming-ingestion-policy-definition"></a>Définition de la politique d’ingestion en continu

La politique d’ingestion en continu peut être définie sur une table ou une base de données. La définition de cette stratégie au niveau de la base de données applique les mêmes paramètres à toutes les tables existantes et futures de la base de données. Si la politique d’ingestion en continu est définie aux niveaux de table et de base de données, le réglage du niveau de table prime.

> [!NOTE]
> Si une table n’est pas directement ingestion en streaming, mais seulement via une stratégie de mise à jour, aucune politique d’ingestion en continu ne doit être définie sur cette table. 

La politique d’ingestion en continu fixe le nombre maximum de magasins en rangée sur lesquels les données de diffusion de la table seront distribuées. La distribution est nécessaire pour la disponibilité et le support tarifaire des données.

## <a name="setting-the-number-of-row-stores"></a>Définir le nombre de magasins à rames

Le nombre de magasins en rangée dans la politique d’ingestion en continu doit être défini. Ce nombre doit être basé sur le taux de données en streaming par tableau (l’estimation approximative est suffisante).
Le nombre minimum recommandé de magasins en rangée pour n’importe quelle table est de quatre. Le nombre maximum de magasins en rangée est de 64.
Plus le taux de données de streaming pour le tableau est élevé, plus le nombre nécessaire de magasins en rangée requis dans la politique d’ingestion de streaming associée est élevé.
Utilisez le tableau suivant pour les paramètres recommandés (en cas de doute utiliser un nombre plus élevé) :

|Taux estimé de données de streaming à l’heure de pointe (par tableau)|Nombre de magasins Row|
|----------|------|
|< 1 Gb/h |4|
|1 - 2 Go/h |4-8|
|2 - 3 Go/h |8-12|
|3 - 4 Go/h |12-16|
| > 4 Go/h |

 Pour plus de conseils à ce sujet, ouvrez un [billet de soutien](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

Pour une latence optimale de requête, le nombre de magasins en rangée définis par tableau ne devrait pas dépasser considérablement la recommandation ci-dessus.

> [!NOTE]
> Lors de la mise en place de la politique d’ingestion en continu pour la base de données, attribuez le nombre de magasins en rangée qui est nécessaire pour la table avec le taux de données le plus élevé. 