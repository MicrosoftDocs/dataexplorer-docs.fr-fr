---
title: Stratégie sandbox-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la stratégie sandbox dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7d56d602f53db29f5ea558acb0e9e4288e5ac6e3
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967483"
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

