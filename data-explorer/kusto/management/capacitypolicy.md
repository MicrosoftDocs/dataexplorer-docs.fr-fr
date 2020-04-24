---
title: Politique de capacité - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique de capacité dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: af648bd0a4b328477b14e20a2457e3e914df2827
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521996"
---
# <a name="capacity-policy"></a>Politique de capacité

Une politique de capacité est utilisée pour contrôler les ressources de calcul qui sont utilisées pour effectuer l’ingestion de données et d’autres opérations de toilettage des données (comme les étendues de fusion).

## <a name="the-capacity-policy-object"></a>L’objet de la politique de capacité

La politique de capacité `IngestionCapacity` `ExtentsMergeCapacity`est `ExtentsPurgeRebuildCapacity` `ExportCapacity`composée de , , et .

### <a name="ingestion-capacity"></a>Capacité d’ingestion

|Propriété                           |Type    |Description                                                                                                                                                                               |
|-----------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |Une valeur maximale pour le nombre d’opérations d’ingestion simultanées dans un cluster                                                                                                            |
|CoreUtilizationCoefficient         |double  |Un coefficient pour le pourcentage de carottes à utiliser lors du calcul de la capacité `ClusterMaximumConcurrentOperations`d’ingestion (le résultat du calcul sera toujours normalisé par ) |                                                                                                                             |

La capacité totale d’ingestion du cluster (comme le montre [la capacité .show)](../management/diagnostics.md#show-capacity)est calculée par :

Minimum`ClusterMaximumConcurrentOperations`( `Number of nodes in cluster` maximum (1, `Core count per node`  *  `CoreUtilizationCoefficient`))

> [!Note] 
> Dans les clusters avec trois nœuds ou plus, le nœud `Number of nodes in cluster` d’administration ne participe pas à effectuer des opérations d’ingestion, est donc réduit de 1.

### <a name="extents-merge-capacity"></a>Étendues Capacité de fusion

|Propriété                           |Type    |Description                                                                                    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |Une valeur maximale pour le nombre d’étendues simultanées fusion/reconstruction des opérations sur un seul nœud |

La capacité totale de fusion des étendues du cluster (comme le montre [la capacité .show)](../management/diagnostics.md#show-capacity)est calculée par :

`Number of nodes in cluster`X`MaximumConcurrentOperationsPerNode`

> [!Note] 
> Dans les clusters avec trois nœuds ou plus, le nœud d’administration ne participe pas à effectuer des opérations de fusion, est donc `Number of nodes in cluster` réduit de 1.

### <a name="extents-purge-rebuild-capacity"></a>Étendues Purge Capacité de reconstruction

|Propriété                           |Type    |Description                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |Une valeur maximale pour le nombre d’étendues simultanées purge des opérations de reconstruction (mesures de reconstruction pour les opérations de purge) sur un seul nœud |

Les étendues totales de la capacité de reconstruction de purge de la grappe (comme le montre [la capacité .show)](../management/diagnostics.md#show-capacity)sont calculées par :

`Number of nodes in cluster`X`MaximumConcurrentOperationsPerNode`

> [!Note] 
> Dans les clusters avec trois nœuds ou plus, le nœud d’administration ne participe pas à effectuer des opérations de fusion, est donc `Number of nodes in cluster` réduit de 1.

### <a name="export-capacity"></a>Capacité d’exportation

|Propriété                           |Type    |Description                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |Une valeur maximale pour le nombre d’opérations d’exportation simultanées dans un cluster.                                                                                                           |
|CoreUtilizationCoefficient         |double  |Un coefficient pour le pourcentage de carottes à utiliser lors du calcul de la `ClusterMaximumConcurrentOperations`capacité d’exportation (le résultat du calcul sera toujours normalisé par ) |

La capacité d’exportation totale du cluster (comme le montre [la capacité .show)](../management/diagnostics.md#show-capacity)est calculée par :

Minimum`ClusterMaximumConcurrentOperations`( `Number of nodes in cluster` maximum (1, `Core count per node`  *  `CoreUtilizationCoefficient`))

> [!Note] 
> Dans les clusters avec trois nœuds ou plus, le nœud d’administration ne participe pas à des opérations d’exportation performantes, est donc `Number of nodes in cluster` réduit de 1.

### <a name="extents-partition-capacity"></a>Étendues capacité de partition

|Propriété                           |Type    |Description                                                                             |
|-----------------------------------|--------|----------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |Une valeur maximale pour le nombre d’étendues simultanées des opérations de partition dans un cluster. |

La capacité totale de partition des étendues de la grappe (comme `ClusterMaximumConcurrentOperations`le montre la capacité [.show)](../management/diagnostics.md#show-capacity)est définie par une seule propriété : .

### <a name="defaults"></a>Valeurs par défaut

La politique de capacité par défaut a la représentation JSON suivante :

```kusto 
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  }
}
```

> [!WARNING]
> Il est **rarement** recommandé de modifier une politique de capacité sans consulter d’abord l’équipe De Kusto.

## <a name="control-commands"></a>Commandes de contrôle

* Utiliser [.show capacité de stratégie de cluster](capacity-policy.md#show-cluster-policy-capacity) pour montrer la politique de capacité actuelle de la grappe.
* Utilisez [la capacité de la politique de cluster .alter](capacity-policy.md#alter-cluster-policy-capacity) pour modifier la politique de capacité du cluster.

## <a name="throttling"></a>Limitation

Kusto limite le nombre de demandes simultanées pour les commandes suivantes :

1. Ingestions (inclut toutes les commandes qui sont répertoriées [ici](../management/data-ingestion/index.md))
      * La limite est telle que définie dans la politique de [capacité](#capacity-policy).
1. Fusions
      * La limite est telle que définie dans la politique de [capacité](#capacity-policy).
1. Purges
      * Global est actuellement fixé à un par cluster.
      * La capacité de reconstruction de purge est utilisée à l’interne pour déterminer le nombre d’opérations de reconstruction simultanées pendant les ordres de purge (les commandes de purge ne seront pas bloquées/étranglées en raison de cela, mais travailleront plus rapidement/plus lentement selon la capacité de reconstruction de purge).
1. Exports
      * La limite est telle que définie dans la politique de [capacité](#capacity-policy).


Lorsque Kusto détecte qu’une opération a dépassé l’opération simultanée autorisée, Kusto répondra avec un code HTTP 429.
Le client doit rejuger l’opération après quelques backoff.