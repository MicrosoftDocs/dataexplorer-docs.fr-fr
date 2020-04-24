---
title: Commandes de contrôle de la politique de capacité - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les commandes de contrôle de la politique de capacité dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 929cfa885a7373b400b832d908677a7a5fb93ef6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522081"
---
# <a name="capacity-policy-control-commands"></a>Commandes de contrôle de la politique de capacité

Les commandes de contrôle suivantes peuvent être utilisées pour gérer la politique de [capacité](../management/capacitypolicy.md)d’un cluster.

Les commandes nécessitent des [autorisations AllDatabasesAdmin.](../management/access-control/role-based-authorization.md)

## <a name="show-cluster-policy-capacity"></a>afficher la capacité de la politique de cluster

```kusto
.show cluster policy capacity
```

Affiche la politique de capacité actuelle pour le cluster.

**Sortie**

|Nom de la stratégie | Nom de l’entité | Stratégie | Entités enfants | Type d’entité
|---|---|---|---|---
|CapacitéPolicy | | Chaîne formatée par JSON qui représente la politique | La liste des bases de données du cluster |Cluster


## <a name="alter-cluster-policy-capacity"></a>modifier la capacité de la politique des clusters

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**Remarque**: Les changements apportés à la politique de capacité des clusters pourraient prendre jusqu’à 1 heure pour entrer en vigueur.

**Exemples :**

* Modification explicite de toutes les propriétés de la politique de cluster :

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

* Modification d’une propriété unique de la politique de niveau de cluster, en gardant toutes les autres propriétés intactes :

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
