---
title: Commandes de contrôle de stratégie de capacité-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les commandes de contrôle de stratégie de capacité dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 512ab14c2abc1f777376d81d4944caf2c3343513
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967330"
---
# <a name="capacity-policy-commands"></a>Commandes de stratégie de capacité

Les commandes de contrôle suivantes peuvent être utilisées pour gérer la [stratégie de capacité](../management/capacitypolicy.md)d’un cluster.

Les commandes requièrent des autorisations [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) .

## <a name="show-cluster-policy-capacity"></a>afficher la capacité de la stratégie de cluster

```kusto
.show cluster policy capacity
```

Affiche la stratégie de capacité actuelle pour le cluster.

**Sortie**

|Nom de la stratégie | Nom de l’entité | Stratégie | Entités enfants | Type d’entité
|---|---|---|---|---
|CapacityPolicy | | Chaîne au format JSON qui représente la stratégie. | Liste des bases de données dans le cluster |Cluster


## <a name="alter-cluster-policy-capacity"></a>modifier la capacité de la stratégie de cluster

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**Remarque**: la prise en compte des modifications apportées à la stratégie de capacité du cluster peut prendre jusqu’à 1 heure.

**Exemples :**

* Modification explicite de toutes les propriétés de la stratégie de cluster :

```kusto
.alter cluster policy capacity
'{'
  '"IngestionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 512,'
    '"CoreUtilizationCoefficient": 0.75'
  '},'
  '"ExtentsMergeCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExtentsPurgeRebuildCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExportCapacity": {'
    '"ClusterMaximumConcurrentOperations": 100,'
    '"CoreUtilizationCoefficient": 0.25'
  '},'
  '"ExtentsPartitionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 4'
  '}'
'}'
```

* Modification d’une seule propriété de la stratégie au niveau du cluster, en conservant toutes les autres propriétés intactes :

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
