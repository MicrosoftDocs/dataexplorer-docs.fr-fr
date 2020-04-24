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
ms.openlocfilehash: 7ac5a92b2084eaf2b447f296be34b2f4e79e1bb7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520126"
---
# <a name="sandbox-policy"></a>Politique Sandbox

Les commandes suivantes permettent la gestion des [bacs à sable](../concepts/sandboxes.md) et des [politiques de bac](sandboxpolicy.md) à sable dans un service moteur Kusto.

Les commandes nécessitent des [autorisations AllDatabasesAdmin.](access-control/role-based-authorization.md)

## <a name="sandbox-policy"></a>Politique Sandbox

### <a name="show-cluster-policy-sandbox"></a>.afficher la politique de cluster sandbox

Affiche toutes les stratégies de bac à sable configurées au niveau du cluster.

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>.modifier la politique de cluster sandbox

Modifie la collecte des politiques sandbox au niveau du cluster.

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

### <a name="drop-cluster-policy-sandbox"></a>.drop cluster politique sandbox

Pour abandonner **toutes les** stratégies sandbox, utilisez la commande suivante :

```kusto
.delete cluster policy sandbox
```

