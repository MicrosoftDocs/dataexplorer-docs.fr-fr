---
title: Politique Sandbox - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique sandbox dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0a59e25c6c38d3189330299af1b19f89357cb456
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520058"
---
# <a name="sandbox-policy"></a>Politique Sandbox

## <a name="overview"></a>Vue d’ensemble

Kusto prend en charge l’exécution de certains plugins dans [les bacs](../concepts/sandboxes.md)à sable , où les ressources disponibles pour le bac à sable sont limitées et contrôlées, à la fois à des fins de sécurité, ainsi qu’à des fins de gouvernance des ressources.

Les bacs à sable sont exécutés sur les nœuds du service moteur Kusto, et certaines de leurs limites sont définies dans les politiques sandbox, où chaque type de bac à sable peut avoir sa propre politique Sandbox.

Les stratégies Sandbox sont gérées au niveau des grappes et affectent tous les nœuds du cluster.

La modification des politiques nécessite des [autorisations AllDatabasesAdmin.](../management/access-control/role-based-authorization.md)

## <a name="the-policy-object"></a>L’objet de la politique

une politique Sandbox a les propriétés suivantes :

* **SandboxKind**: définit le type de bac à `PythonExecution` `RExecution`sable (par exemple , ).
* **IsEnabled**: définit si les bacs à sable de ce type sont activés ou non sur les nœuds du cluster.
* **TargetCountPerNode**: définit le nombre de bacs à sable de ce type sont autorisés à fonctionner sur les nœuds du cluster.
  * Les valeurs peuvent être entre 1 et deux fois le nombre de processeurs par nœud.
  * La valeur par défaut est `16`.
* **MaxCpuRatePerSandbox**: définit le taux maximum de processeur en pourcentage de tous les cœurs disponibles qu’un seul bac à sable peut utiliser.
  * Les valeurs peuvent être comprises entre 1 et 100.
  * La valeur par défaut est `50`.
* **MaxMemoryMbPerSandbox**: définit la quantité maximale de mémoire (en mégaoctets) qu’un seul bac à sable peut utiliser.
  * Les valeurs peuvent être comprises entre 200 et 65536 (64 Go).
  * La valeur `20480` par défaut est de 20 Go.

## <a name="example"></a>Exemple

La politique suivante fixe des limites différentes pour `PythonExecution` `RExecution`2 types différents de bacs à sable - et :

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

## <a name="notes"></a>Notes

* Les modifications apportées à la stratégie sandbox s’appliquent aux sanboxes créées à partir du moment où la modification est appliquée.
  * Les bacs à sable qui ont été pré-alloués avant le changement de politique continueront de fonctionner selon les limites de politique précédentes, jusqu’à ce qu’ils soient utilisés dans le cadre d’une requête.
* Il pourrait y avoir un délai pouvant aller jusqu’à 5 minutes jusqu’à ce que le changement de politique entre en vigueur, car les nœuds de grappes se demandent périodiquement des changements de politique.

## <a name="next-steps"></a>Étapes suivantes

Utilisez les commandes de [contrôle de la politique sandbox](../management/sandbox-policy.md) pour gérer la politique sandbox du cluster.