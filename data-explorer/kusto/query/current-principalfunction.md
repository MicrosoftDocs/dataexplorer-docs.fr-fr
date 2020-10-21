---
title: current_principal ()-Azure Explorateur de données
description: Cet article décrit current_principal () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a2530b93bd5102b7c21aa535eb8e7ca7c4360ddf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252494"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Retourne le nom principal actuel qui exécute la requête.

## <a name="syntax"></a>Syntaxe

`current_principal()`

## <a name="returns"></a>Retours

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
