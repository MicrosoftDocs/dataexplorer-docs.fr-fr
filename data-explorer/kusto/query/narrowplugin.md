---
title: plug-in étroit-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le plug-in étroit dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e597a2467da21a2c9e83aba28a1e83b242f61c75
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346675"
---
# <a name="narrow-plugin"></a>narrow, plug-in

```kusto
T | evaluate narrow()
```

Le `narrow` plug-in « dépivote » un tableau étendu dans une table avec seulement trois colonnes : le numéro de ligne, le type de colonne et la valeur de colonne (comme `string` ).

Le `narrow` plug-in est conçu principalement à des fins d’affichage, car il permet d’afficher confortablement des tables larges sans avoir besoin de faire défiler horizontalement.

## <a name="syntax"></a>Syntaxe

`T | evaluate narrow()`

## <a name="examples"></a>Exemples

L’exemple suivant montre un moyen simple de lire la sortie de la `.show diagnostics` commande de contrôle Kusto.

```kusto
.show diagnostics
 | evaluate narrow()
```

Le résultat de `.show diagnostics` lui-même est une table avec une seule ligne et 33 colonnes. En utilisant le `narrow` plug-in, nous allons « faire pivoter » la sortie comme suit :

Ligne  | Colonne                              | Valeur
-----|-------------------------------------|-----------------------------
0    | IsHealthy                           | Vrai
0    | IsRebalanceRequired                 | Faux
0    | IsScaleOutRequired                  | Faux
0    | MachinesTotal                       | 2
0    | MachinesOffline                     | 0
0    | NodeLastRestartedOn                 | 2017-03-14 10:59:18.9263023
0    | AdminLastElectedOn                  | 2017-03-14 10:58:41.6741934
0    | ClusterWarmDataCapacityFactor       | 0.130552847673333
0    | ExtentsTotal                        | 136
0    | DiskColdAllocationPercentage        | 5
0    | InstancesTargetBasedOnDataCapacity  | 2
0    | TotalOriginalDataSize               | 5167628070
0    | TotalExtentSize                     | 1779165230
0    | IngestionsLoadFactor                | 0
0    | IngestionsInProgress                | 0
0    | IngestionsSuccessRate               | 100
0    | MergesInProgress                    | 0
0    | BuildVersion                        | 1.0.6281.19882
0    | BuildTime                           | 2017-03-13 11:02:44.0000000
0    | ClusterDataCapacityFactor           | 0.130552847673333
0    | IsDataWarmingRequired               | Faux
0    | RebalanceLastRunOn                  | 2017-03-21 09:14:53.8523455
0    | DataWarmingLastRunOn                | 2017-03-21 09:19:54.1438800
0    | MergesSuccessRate                   | 100
0    | NotHealthyReason                    | nul
0    | IsAttentionRequired                 | Faux
0    | AttentionRequiredReason             | nul
0    | ProductVersion                      | KustoRelease_2017.03.13.2
0    | FailedIngestOperations              | 0
0    | FailedMergeOperations               | 0
0    | MaxExtentsInSingleTable             | 64
0    | TableWithMaxExtents                 | KustoMonitoringPersistentDatabase.KustoMonitoringTable
0    | WarmExtentSize                      | 1779165230