---
title: current_principal_is_member_of ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit current_principal_is_member_of () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4b6f7d0b9ab4074f16ca00b4a3febb1a17351736
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737757"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

Vérifie l’appartenance au groupe ou l’identité principale du principal actuel exécutant la requête.

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

* *liste d’expressions* : liste de littéraux de chaîne séparés par des virgules, où chaque littéral est une chaîne de nom complet (FQN) principale formée comme suit :  
*PrinciplaType*`=`*PrincipalId*PrincipalId`;`*TenantId*

| PrincipalType   | Préfixe FQN  |
|-----------------|-------------|
| Utilisateur AAD        | `aaduser=`  |
| Groupe AAD       | `aadgroup=` |
| Application AAD | `aadapp=`   |

**Retourne**

La fonction retourne :
* `true`: si le principal en cours d’exécution de la requête a été correctement mis en correspondance pour au moins un argument d’entrée.
* `false`: si le principal en cours d’exécution de la requête n’est `aadgroup=` membre d’aucun argument FQN et n’est pas égal `aaduser=` à `aadapp=` l’un des arguments ou FQN.
* `(null)`: si le principal en cours d’exécution de la requête n’est `aadgroup=` membre d’aucun argument FQN et n’est pas égal `aaduser=` à `aadapp=` l’un des arguments ou FQN, et qu’au moins un argument FQN n’a pas été résolu avec succès (n’a pas été ajouté à AAD). 

> [!NOTE]
> Étant donné que la fonction retourne une valeur à trois`true`États `false`(, `null`et), il est important de s’appuyer uniquement sur les valeurs de retour positives pour confirmer la réussite de l’appartenance. En d’autres termes, les expressions suivantes ne sont pas les mêmes :
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

Utilisation d’un tableau dynamique au lieu d’arguments de multiple :

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

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
