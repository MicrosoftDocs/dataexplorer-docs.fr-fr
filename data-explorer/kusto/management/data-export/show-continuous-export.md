---
title: Afficher l’exportation continue des données-Azure Explorateur de données
description: Cet article explique comment afficher les propriétés d’exportation de données en continu dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: db7583c055468808ade1166c0bfee90859399d32
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149514"
---
# <a name="show-continuous-export"></a>Afficher l’exportation continue

Retourne les propriétés d’exportation continue de *ContinuousExportName*. 

## <a name="syntax"></a>Syntaxe

`.show` `continuous-export` *ContinuousExportName*

## <a name="properties"></a>Propriétés

| Propriété             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Nom de l’exportation continue. |

`.show` `continuous-exports`

Retourne toutes les exportations continues de la base de données. 

## <a name="output"></a>Output

| Paramètre de sortie    | Type     | Description                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| CursorScopedTables  | String   | Liste des tables (fait) explicitement délimitées (sérialisées JSON)               |
| ExportProperties    | String   | Propriétés d’exportation (sérialisées JSON)                                     |
| ExportedTo          | DateTime | Dernière date et heure d’ingestion qui a été exportée avec succès       |
| ExternalTableName   | String   | Nom de la table externe                                              |
| ForcedLatency       | TimeSpan | Latence forcée (null si non fourni)                                   |
| IntervalBetweenRuns | TimeSpan | Intervalle entre les exécutions                                                   |
| IsDisabled          | Boolean  | True si l’exportation continue est désactivée                               |
| IsRunning           | Boolean  | True si l’exportation continue est en cours d’exécution                      |
| LastRunResult       | String   | Résultats de la dernière exécution d’exportation continue ( `Completed` ou `Failed` ) |
| LastRunTime         | DateTime | Heure de la dernière exécution de l’exportation continue (heure de début)           |
| Nom                | String   | Nom de l’exportation continue                                           |
| Requête               | String   | Exporter une requête                                                            |
| StartCursor         | String   | Point de départ de la première exécution de cette exportation continue         |

