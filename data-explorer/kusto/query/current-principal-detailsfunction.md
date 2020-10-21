---
title: current_principal_details ()-Azure Explorateur de données
description: Cet article décrit current_principal_details () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: 387504d49b2c8be52357be74e69cdbde581c1298
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252529"
---
# <a name="current_principal_details"></a>current_principal_details()

Retourne les détails du principal qui exécute la requête.

## <a name="syntax"></a>Syntaxe

`current_principal_details()`

## <a name="returns"></a>Retours

Détails du principal actuel sous la forme d’un `dynamic` .

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  « UserPrincipalName » : « user@fabrikam.com »,<br>  « IdentityProvider » : « https://sts.windows.net »,<br>  « Authority » : « 72f988bf-86f1-41AF-91ab-2d7cd011db47 »,<br>  « MFA » : « true »,<br>  « Type » : « AadUser »,<br>  « DisplayName » : « James Smith (UPN : user@fabrikam.com ) »,<br>  « ObjectId » : « 346e950e-4a62-42BF-96f5-4cf4eac3f11e »,<br>  « FQN » : null,<br>  « Remarques » : null<br>}|
