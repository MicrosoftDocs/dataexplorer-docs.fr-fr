---
title: current_principal_is_member_of() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit current_principal_is_member_of() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6d33fd19181e1eba93b54f684864fed062701307
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663843"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

Vérifie l’appartenance au groupe ou l’identité principale du principal actuel qui exécute la requête.

```kusto
print current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

**Syntaxe**

`current_principal_is_member_of`(`*list of string literals*`)

**Arguments**

* *liste d’expressions* - une liste de virgules séparées de la chaîne littérale, où chaque littérale est un principal nom entièrement qualifié (FQN) chaîne formée comme:  
*PrinciplaType*`=`*PrincipalId*`;`*TenantId*

| PrincipalType (principalType)   | Préfixe FQN  |
|-----------------|-------------|
| Utilisateur AAD        | `aaduser=`  |
| Groupe AAD       | `aadgroup=` |
| Demande AAD | `aadapp=`   |

**Retourne**

La fonction retourne :
* `true`: si le principal actuel exécutant la requête a été jumelé avec succès pour au moins un argument d’entrée.
* `false`: si le principal actuel qui dirigeait `aadgroup=` la requête n’était pas membre `aaduser=` `aadapp=` d’arguments de la FQN et n’était égal à aucun des arguments ou à la FQN.
* `(null)`: si le principal actuel qui dirigeait `aadgroup=` la requête n’était pas membre `aaduser=` `aadapp=` d’arguments de la FQN et n’était pas égal à l’un ou l’autre des arguments de la FQN, et qu’au moins un argument de la FQN n’a pas été résolu avec succès (n’a pas été préséqué dans aAD). 

> [!NOTE]
> Parce que la fonction retourne`true`une `false`valeur `null`à trois États ( , , et ), il est important de compter uniquement sur des valeurs de rendement positif pour confirmer le succès de l’adhésion. En d’autres termes, les expressions suivantes ne sont pas les mêmes :
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


**Exemple**

```kusto
print result=current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

| result |
|--------|
| (Null) |

Utilisation d’un tableau dynamique au lieu d’arguments multple:

```kusto
print result=current_principal_is_member_of(
    dynamic([
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    ]))
```

| result |
|--------|
| (Null) |

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end