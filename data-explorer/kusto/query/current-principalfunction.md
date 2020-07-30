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
ms.openlocfilehash: 7fa1ad900eb91390436e88c44ad9fd7394ad087d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348647"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Retourne le nom principal actuel qui exécute la requête.

## <a name="syntax"></a>Syntaxe

`current_principal()`

## <a name="returns"></a>Retourne

Nom complet du principal actuel (FQN) en tant que `string` .  
Le format de chaîne est le suivant :  
*PrinciplaType* `=` *PrincipalId* `;` *TenantId*

## <a name="example"></a>Exemple

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
