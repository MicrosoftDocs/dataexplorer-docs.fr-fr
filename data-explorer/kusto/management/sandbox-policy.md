---
title: Stratégie sandbox-Azure Explorateur de données | Microsoft Docs
description: En savoir plus sur les commandes de stratégie sandbox dans Azure Explorateur de données. Consultez Comment afficher, ajuster et supprimer des stratégies de bac à sable (sandbox).
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ba37147db87c436aff7d77790641b17576e86392
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002966"
---
# <a name="sandbox-policy-command"></a>commande de stratégie sandbox

Les commandes suivantes permettent de gérer les [bacs à sable (sandbox)](../concepts/sandboxes.md) et les [stratégies de bac à sable (sandbox)](sandboxpolicy.md) dans un service de moteur Kusto.

Les commandes requièrent des autorisations [AllDatabasesAdmin](access-control/role-based-authorization.md) .

## <a name="sandbox-policy"></a>Stratégie de bac à sable

### <a name="show-cluster-policy-sandbox"></a>. afficher le bac à sable de stratégie de cluster

Affiche toutes les stratégies sandbox configurées au niveau du cluster.

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>. modification du bac à sable de la stratégie de cluster

Modifie la collection de stratégies de bac à sable (sandbox) au niveau du cluster.

```kusto
.alter cluster policy sandbox @'['
  '{'
    '"SandboxKind": "PythonExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '},'
  '{'
    '"SandboxKind": "RExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '}'
']'
```

### <a name="drop-cluster-policy-sandbox"></a>. supprimer le bac à sable de stratégie de cluster

Pour supprimer **toutes les** stratégies de bac à sable (sandbox), utilisez la commande suivante :

```kusto
.delete cluster policy sandbox
```
