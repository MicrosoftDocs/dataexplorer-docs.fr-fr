---
title: plugin étroit - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le plugin étroit dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75b211f32c15eefc60ca40b0408345be4a656652
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512238"
---
# <a name="narrow-plugin"></a>plugin étroit

```kusto
T | evaluate narrow()
```

Le `narrow` plugin "unpivots" une table large dans une table avec seulement trois colonnes: numéro de ligne, type de colonne, et la valeur de colonne (comme `string`).

Le `narrow` plugin est conçu principalement à des fins d’affichage, car il permet d’afficher des tables larges confortablement sans avoir besoin de défilement horizontal.

**Syntaxe**

`T | evaluate narrow()`

**Exemples**

L’exemple suivant montre un moyen facile de `.show diagnostics` lire la sortie de la commande de contrôle Kusto.

```kusto
.show diagnostics
 | evaluate narrow()
```

Les résultats `.show diagnostics` de lui-même est une table avec une seule rangée et 33 colonnes. En utilisant `narrow` le plugin nous "tournons" la sortie à quelque chose comme ceci:

Ligne  | Colonne                              | Valeur
-----|-------------------------------------|-----------------------------
0    | IsHealthy (en)                           | True
0    | IsRebalanceRequired                 | False
0    | IsScaleOutRequired                  | False
0    | MachinesTotal                       | 2
0    | MachinesOffline                     | 0
0    | NodeLastRestartedOn                 | 2017-03-14 10:59:18.9263023
0    | AdminLastElectedOn                  | 2017-03-14 10:58:41.6741934
0    | ClusterWarmDataCapacityFactor ClusterWarmDataCapacityFactor ClusterWarmDataCapacityFactor ClusterWar       | 0.130552847673333
0    | ExtentsTotal                        | 136
0    | DiskColdAllocationPercentage        | 5
0    | InstancesTargetBasedOnDataCapacity  | 2
0    | TotalOriginalDataSize TotalOriginalDataSize TotalOriginalDataSize TotalOri               | 5167628070
0    | TotalExtentSize                     | 1779165230
0    | IngestionsLoadFactor                | 0
0    | IngestionsInProgresse                | 0
0    | IngestionsSuccessRate               | 100
0    | FusionsInProgress                    | 0
0    | BuildVersion                        | 1.0.6281.19882
0    | BuildTime (en)                           | 2017-03-13 11:02:44.0000000
0    | ClusterDataCapacityFactor           | 0.130552847673333
0    | IsDataWarmingRequired               | False
0    | RebalanceLastRunOn                  | 2017-03-21 09:14:53.8523455
0    | DataWarmingLastRunOn                | 2017-03-21 09:19:54.1438800
0    | FusionsSuccessRate                   | 100
0    | PasHealthyReason                    | [null]
0    | IsAttentionRequired                 | False
0    | AttentionRequiredReason             | [null]
0    | ProductVersion                      | KustoRelease_2017.03.13.2
0    | Échec Des opératations              | 0
0    | Échec Desoperations               | 0
0    | MaxExtentsInSingleTable             | 64
0    | TableWithMaxExtents                 | KustoMonitoringPersistentDatabase.KustoMonitoringTable
0    | WarmExtentSize                      | 1779165230