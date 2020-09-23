---
title: Stratégie de capacité-Azure Explorateur de données
description: Cet article décrit la stratégie de capacité dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 6b0c1247c0d161caae3301dc674a701bd1019ad3
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91056932"
---
# <a name="capacity-policy"></a>Stratégie de capacité 

Une stratégie de capacité est utilisée pour contrôler les ressources de calcul des opérations de gestion des données sur le cluster.

## <a name="the-capacity-policy-object"></a>Objet de stratégie de capacité

La stratégie de capacité est composée des éléments suivants :

* [IngestionCapacity](#ingestion-capacity)
* [ExtentsMergeCapacity](#extents-merge-capacity)
* [ExtentsPurgeRebuildCapacity](#extents-purge-rebuild-capacity)
* [ExportCapacity](#export-capacity)
* [ExtentsPartitionCapacity](#extents-partition-capacity)

## <a name="ingestion-capacity"></a>Capacité d’ingestion

|Propriété       |Type    |Description    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |Valeur maximale pour le nombre d’opérations simultanées d’ingestion dans un cluster.               |
|CoreUtilizationCoefficient         |double  |Un coefficient pour le pourcentage de cœurs à utiliser lors du calcul de la capacité d’ingestion. Le résultat du calcul sera toujours normalisé par `ClusterMaximumConcurrentOperations` <br> La capacité d’ingestion totale du cluster, comme indiqué par la fonction [. afficher la capacité](../management/diagnostics.md#show-capacity), est calculée par : <br> Minimum ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * maximum (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> Dans les clusters comportant trois nœuds ou plus, le nœud d’administration ne participe pas aux opérations d’ingestion. Le `Number of nodes in cluster` est réduit d’une unité.

## <a name="extents-merge-capacity"></a>Capacité de fusion des étendues

|Propriété                           |Type    |Description                                                                                                |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------------------|
|MinimumConcurrentOperationsPerNode |long    |Valeur minimale pour le nombre d’opérations de fusion/régénération d’étendues simultanées sur un nœud unique. La valeur par défaut est 1 |
|MaximumConcurrentOperationsPerNode |long    |Valeur maximale pour le nombre d’opérations de fusion/régénération d’étendues simultanées sur un nœud unique. La valeur par défaut est 3 |

La capacité de fusion totale des étendues du cluster, comme indiqué par la fonction [. afficher la capacité](../management/diagnostics.md#show-capacity), est calculée par :

`Number of nodes in cluster` x `Concurrent operations per node`

La valeur effective de `Concurrent operations per node` est automatiquement ajustée par le système dans la plage [ `MinimumConcurrentOperationsPerNode` , `MaximumConcurrentOperationsPerNode` ], tant que le taux de réussite des opérations de fusion est supérieur à 90%.

> [!Note]
> Dans les clusters comportant trois nœuds ou plus, le nœud d’administration ne participe pas aux opérations de fusion. Le `Number of nodes in cluster` est réduit d’une unité.

## <a name="extents-purge-rebuild-capacity"></a>Étendues purger la capacité de reconstruction

|Propriété                           |Type    |Description                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |Valeur maximale pour le nombre d’extensions de régénération simultanées pour les opérations de vidage sur un nœud unique |

Les étendues totales du cluster purgent la capacité de reconstruction (comme indiqué par [. afficher la capacité](../management/diagnostics.md#show-capacity)) est calculée par :

`Number of nodes in cluster` x `MaximumConcurrentOperationsPerNode`

> [!Note]
> Dans les clusters comportant trois nœuds ou plus, le nœud d’administration ne participe pas aux opérations de fusion. Le `Number of nodes in cluster` est réduit d’une unité.

## <a name="export-capacity"></a>Capacité d’exportation

|Propriété                           |Type    |Description                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |Valeur maximale pour le nombre d’opérations d’exportation simultanées dans un cluster.                                           |
|CoreUtilizationCoefficient         |double  |Coefficient pour le pourcentage de cœurs à utiliser lors du calcul de la capacité d’exportation. Le résultat du calcul sera toujours normalisé par `ClusterMaximumConcurrentOperations` . |

La capacité d’exportation totale du cluster, comme indiqué par [. afficher la capacité](../management/diagnostics.md#show-capacity), est calculée par :

Minimum ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * maximum (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> Dans les clusters comportant trois nœuds ou plus, le nœud d’administration ne participe pas aux opérations d’exportation. Le `Number of nodes in cluster` est réduit d’une unité.

## <a name="extents-partition-capacity"></a>Capacité de partition des étendues

|Propriété                           |Type    |Description                                                                                         |
|-----------------------------------|--------|----------------------------------------------------------------------------------------------------|
|ClusterMinimumConcurrentOperations |long    |Valeur minimale pour le nombre d’opérations de partition d’étendues simultanées dans un cluster. Valeur par défaut : 1  |
|ClusterMaximumConcurrentOperations |long    |Valeur maximale pour le nombre d’opérations de partition d’étendues simultanées dans un cluster. Valeur par défaut : 16 |

Les étendues totales de la partition du cluster (comme indiqué par [. Affichez la capacité](../management/diagnostics.md#show-capacity)).

La valeur effective de `Concurrent operations` est automatiquement ajustée par le système dans la plage [ `ClusterMinimumConcurrentOperations` , `ClusterMaximumConcurrentOperations` ], tant que le taux de réussite des opérations de partitionnement est supérieur à 90%.

## <a name="materialized-views-capacity-policy"></a>Stratégie de capacité des vues matérialisées

Modifiez la stratégie de capacité à l’aide de la [capacité ALTER Cluster Policy](capacity-policy.md#alter-cluster-policy-capacity). Cette modification nécessite des `AllDatabasesAdmin` autorisations.
La stratégie peut être utilisée pour modifier les paramètres de concurrence pour les vues matérialisées. Cette modification peut être nécessaire lorsqu’il existe plus d’une vue matérialisée définie sur un cluster, et que le cluster ne peut pas suivre la matérialisation de toutes les vues. Par défaut, les paramètres de concurrence sont relativement faibles pour garantir que la matérialisation n’affecte pas les performances du cluster.

> [!WARNING]
> La stratégie de capacité de vue matérialisée ne doit être augmentée que si les ressources du cluster sont correctes (processeur faible, mémoire disponible). L’extension de ces valeurs lorsque les ressources sont limitées peut entraîner l’épuisement des ressources et n’a pas d’impact sur les performances du cluster.

La stratégie de capacité des vues matérialisées fait partie de la [stratégie de capacité](#capacity-policy)du cluster et possède la représentation JSON suivante :

<!-- csl -->
``` 
{
   "MaterializedViewsCapacity": {
    "ClusterMaximumConcurrentOperations": 1,
    "ExtentsRebuildCapacity": {
      "ClusterMaximumConcurrentOperations": 50,
      "MaximumConcurrentOperationsPerNode": 5
    }
  }
}
```

### <a name="properties"></a>Propriétés

Propriété | Description
|---|---|
|`ClusterMaximumConcurrentOperations` | Nombre maximal de vues matérialisées que le cluster peut matérialiser simultanément. Cette valeur est 1 par défaut, tandis que la matérialisation elle-même (d’une seule vue individuelle) peut exécuter plusieurs opérations simultanées. S’il y a plus d’une vue matérialisée définie sur le cluster et que les ressources du cluster sont en bon état, il est recommandé d’augmenter cette valeur. |
| `ExtentsRebuildCapacity`|  Détermine le nombre d’opérations de reconstruction d’étendues simultanées, exécutées pour toutes les vues matérialisées pendant le processus de matérialisation. Si plusieurs vues s’exécutent simultanément, car `ClusterMaximumConcurrentOperation` est supérieur à 1, elles partagent le quota défini par cette propriété. Le nombre maximal d’opérations de reconstruction d’étendues simultanées ne dépasse pas cette valeur. |

### <a name="extents-rebuild"></a>Régénération des extensions

Pour en savoir plus sur les opérations de reconstruction des extensions, consultez fonctionnement des [vues matérialisées](materialized-views/materialized-view-overview.md#how-materialized-views-work). Le nombre maximal d’étendues Rebuild est calculé par :
    
```kusto
Maximum(`ClusterMaximumConcurrentOperations`, `Number of nodes in cluster` * `MaximumConcurrentOperationsPerNode`)
```

* Les valeurs par défaut sont 50 régénérations d’accès concurrentiel total et 5 maximum par nœud.
* La `ExtentsRebuildCapacity` stratégie sert uniquement de limite supérieure. La valeur réelle utilisée est déterminée de façon dynamique par le système, en fonction des conditions du cluster actuel (mémoire, UC) et d’une estimation de la quantité de ressources requises par l’opération de reconstruction. Dans la pratique, la concurrence peut être nettement inférieure à la valeur spécifiée dans la stratégie de capacité.
    * La `MaterializedViewExtentsRebuild` métrique fournit des informations sur le nombre d’extensions qui ont été reconstruites dans chaque cycle de matérialisation. Pour plus d’informations, consultez [analyse des vues matérialisées](materialized-views/materialized-view-overview.md#materialized-views-monitoring).

## <a name="defaults"></a>Valeurs par défaut

La stratégie de capacité par défaut a la représentation JSON suivante :

```json
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MinimumConcurrentOperationsPerNode": 1,
    "MaximumConcurrentOperationsPerNode": 3
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  },
  "ExtentsPartitionCapacity": {
    "ClusterMinimumConcurrentOperations": 1,
    "ClusterMaximumConcurrentOperations": 16
  }
}
```

## <a name="control-commands"></a>Commandes de contrôle

> [!WARNING]
> Consultez l’équipe de Explorateur de données Azure avant de modifier une stratégie de capacité.

* Utilisez la fonctionnalité [. afficher la capacité](capacity-policy.md#show-cluster-policy-capacity) de la stratégie de cluster pour afficher la stratégie de capacité actuelle du cluster.

* Utilisez la capacité de la [stratégie de cluster ALTER](capacity-policy.md#alter-cluster-policy-capacity) pour modifier la stratégie de capacité du cluster.

## <a name="throttling"></a>Limitation

Kusto limite le nombre de demandes simultanées pour les commandes initiées par l’utilisateur suivantes :

* Ingestions (comprend toutes les commandes répertoriées [ici](../../ingest-data-overview.md))
   * La limite est définie dans la [stratégie de capacité](#capacity-policy).
* Purge
   * La globalisation est actuellement fixée à un par cluster.
   * La capacité de reconstruction de vidage est utilisée en interne pour déterminer le nombre d’opérations de reconstruction simultanées pendant les commandes de vidage. Les commandes de vidage ne sont pas bloquées/limitées en raison de ce processus, mais elles fonctionnent plus rapidement ou plus lentement en fonction de la capacité de régénération de purge.
* Exports
   * La limite est définie dans la [stratégie de capacité](#capacity-policy).

Lorsque le cluster détecte qu’une opération a dépassé l’opération simultanée autorisée, il répond avec un code HTTP 429, « limité ».
Réessayez l’opération après un certain intervalle.
