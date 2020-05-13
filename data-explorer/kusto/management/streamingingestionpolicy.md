---
title: Stratégie d’ingestion de streaming-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la stratégie d’ingestion de streaming dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: a0141482e3714ed710dbdc6fa00e3b8b396e77e6
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373305"
---
# <a name="streaming-ingestion-policy"></a>Stratégie d’ingestion de streaming

## <a name="streaming-ingestion-target-scenario"></a>Scénario de cible d’ingestion de streaming

L’ingestion de streaming cible les scénarios qui nécessitent une faible latence avec une durée d’ingestion de moins de 10 secondes pour des volumes de données variés. Il est utilisé pour optimiser le traitement opérationnel de nombreuses tables dans une ou plusieurs bases de données, où le flux de données dans chaque table est relativement faible (peu d’enregistrements par seconde), mais le volume global d’ingestion de données est élevé (des milliers d’enregistrements par seconde).

Utilisez l’ingestion classique (en bloc) à la place de l’ingestion de streaming lorsque la quantité de données dépasse 1 Mo par seconde par table. 

* Pour savoir comment implémenter cette fonctionnalité, consultez ingestion de la [diffusion en continu](../../ingest-data-streaming.md).
* Pour plus d’informations sur les commandes de contrôle d’ingestion de streaming, consultez [commandes de contrôle utilisées pour gérer la stratégie d’ingestion de diffusion en continu](../management/streamingingestion-policy.md)

## <a name="streaming-ingestion-policy-definition"></a>Définition de la stratégie d’ingestion de streaming

La stratégie d’ingestion de streaming peut être définie sur une table ou une base de données. La définition de cette stratégie au niveau de la base de données applique les mêmes paramètres à toutes les tables existantes et futures de la base de données. Si la stratégie d’ingestion de streaming est définie au niveau de la table et de la base de données, le paramètre de niveau table est prioritaire.

> [!NOTE]
> Si une table n’obtient pas directement l’ingestion de la diffusion en continu, mais uniquement par le biais d’une stratégie de mise à jour, aucune stratégie d’ingestion de diffusion en continu ne doit être définie sur cette table. 

La stratégie d’ingestion de streaming définit le nombre maximal de magasins de lignes sur lesquels les données de streaming de la table seront distribuées. La distribution est nécessaire pour la disponibilité et la prise en charge du taux de données.

## <a name="setting-the-number-of-row-stores"></a>Définition du nombre de magasins de lignes

Le nombre de magasins de lignes défini dans la stratégie d’ingestion de streaming doit être défini. Ce nombre doit être basé sur la vitesse de transmission des données par table (une estimation approximative est suffisante).
Le nombre minimal recommandé de magasins de lignes pour une table est de quatre. Le nombre maximal de magasins de lignes pris en charge est de 64.
Plus le débit de données de streaming est élevé pour la table, plus le nombre de magasins de lignes nécessaire dans la stratégie d’ingestion de streaming associée est élevé.
Utilisez le tableau suivant pour les paramètres recommandés (en cas de doute, utilisez un numéro plus élevé) :

|Taux de données de diffusion en continu des heures de pointe estimées (par table)|Nombre de magasins de lignes|
|----------|------|
|< 1 Go/h |4|
|1-2 Go/h |4-8|
|2-3 Go/h |8-12|
|3-4 Go/h |12-16|
| > 4 Go/h |

 Pour obtenir des conseils supplémentaires à ce sujet, ouvrez un [ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

Pour une latence optimale des requêtes, le nombre de banques de lignes définies par table ne doit pas dépasser de manière significative la recommandation ci-dessus.

> [!NOTE]
> Lors de la définition d’une stratégie d’ingestion de streaming pour la base de données, attribuez le nombre de magasins de lignes requis pour la table avec le débit de données le plus élevé. 