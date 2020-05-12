---
title: current_principal ()-Azure Explorateur de données
description: Cet article décrit current_principal () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b1d45fb8b0749a4be30854dd9b0120a7eb127bf2
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227296"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Retourne le nom principal actuel de l’exécution de la requête.

**Syntaxe**

`current_principal()`

**Retourne**

Nom complet du principal actuel (FQN) en tant que `string` .  
La chaîne est formée comme suit :  
*PrinciplaType* `=` *PrincipalId* `;` *TenantId*

**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print fqn=current_principal()
```

|Fqn|
|---|
|aaduser = 346e950e-4a62-42BF-96f5-4cf4eac3f11e ; 72f988bf-86f1-41AF-91ab-2d7cd011db47|

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
