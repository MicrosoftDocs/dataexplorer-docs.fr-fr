---
title: Stratégie sandbox-Azure Explorateur de données
description: Cet article décrit la stratégie sandbox dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 786f771878a7216b62dce127391f0e7967e954f6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512451"
---
# <a name="sandbox-policy"></a>Stratégie de bac à sable

Azure Explorateur de données exécute certains plug-ins dans des [bacs à sable](../concepts/sandboxes.md) dont les ressources disponibles sont limitées et contrôlées pour la sécurité et la gouvernance des ressources.

Les bacs à sable s’exécutent sur les nœuds du moteur Kusto. Certaines de leurs limites sont définies dans les stratégies de bac à sable (sandbox), où chaque type de bac à sable peut avoir sa propre stratégie.

Les stratégies sandbox sont gérées au niveau du cluster et affectent tous les nœuds du cluster.

Pour modifier les stratégies, vous devez disposer des autorisations [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) .

## <a name="the-policy-object"></a>Objet de stratégie

Une stratégie de bac à sable (sandbox) a les propriétés suivantes.

* **SandboxKind**: définit le type du bac à sable (par exemple, `PythonExecution` ,, `RExecution` ).
* **IsEnabled**: définit si les bacs à sable (sandbox) de ce type peuvent s’exécuter sur les nœuds du cluster.
* **TargetCountPerNode**: définit le nombre de bacs à sable (sandbox) de ce type autorisés à s’exécuter sur les nœuds du cluster.
  * Les valeurs peuvent être comprises entre 1 et deux fois le nombre de processeurs par nœud.
  * La valeur par défaut est 16.
* **MaxCpuRatePerSandbox**: définit le débit maximal de l’UC en pourcentage de tous les cœurs disponibles qu’un seul bac à sable (sandbox) peut utiliser.
  * Les valeurs peuvent être comprises entre 1 et 100.
  * La valeur par défaut est 50.
* **MaxMemoryMbPerSandbox**: définit la quantité maximale de mémoire (en mégaoctets) pouvant être utilisée par un seul bac à sable (sandbox).
  * Les valeurs peuvent être comprises entre 200 et 65536 (64 Go).
  * La valeur par défaut est 20480 (20 Go).

## <a name="example"></a>Exemple

La stratégie suivante définit différentes limites pour `PythonExecution` et les `RExecution` bacs à sable (sandbox) :

```json
[
  {
    "SandboxKind": "PythonExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 4,
    "MaxCpuRatePerSandbox": 55,
    "MaxMemoryMbPerSandbox": 65536
  },
  {
    "SandboxKind": "RExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 2,
    "MaxCpuRatePerSandbox": 50,
    "MaxMemoryMbPerSandbox": 10240
  }
]
```

> [!NOTE]
> * Les modifications apportées à la stratégie sandbox s’appliquent aux bacs à sable créés à partir du moment où la modification est appliquée. Les bacs à sable (sandbox) qui ont été pré-alloués avant la modification de la stratégie, continuent à s’exécuter conformément aux limites de stratégie précédentes, jusqu’à ce qu’ils soient utilisés dans le cadre d’une requête.
> * Il peut y avoir un délai de cinq minutes maximum jusqu’à ce que la modification de la stratégie prenne effet, car les nœuds de cluster interrogent régulièrement les modifications de stratégie.

## <a name="next-steps"></a>Étapes suivantes

Utilisez les [commandes de contrôle de stratégie sandbox](../management/sandbox-policy.md) pour gérer la stratégie de bac à sable (sandbox) du cluster.
 
