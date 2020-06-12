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
ms.openlocfilehash: 43e92edd74861acc8207a855243f9ec1e012070a
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717357"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Retourne le nom principal actuel qui exécute la requête.

**Syntaxe**

`current_principal()`

**Retourne**

Nom complet du principal actuel (FQN) en tant que `string` .  
Le format de chaîne est le suivant :  
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
