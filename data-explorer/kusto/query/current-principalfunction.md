---
title: current_principal ()-Azure Explorateur de données | Microsoft Docs
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
ms.openlocfilehash: 0561ac200105015e6d1c1cce1c16fe5f60fc2ccf
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737706"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Retourne le nom principal actuel de l’exécution de la requête.

**Syntaxe**

`current_principal()`

**Retourne**

Nom complet du principal actuel (FQN) en tant que `string`.  
La chaîne est formée comme suit :  
*PrinciplaType*`=`*PrincipalId*PrincipalId`;`*TenantId*

**Exemple**

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
