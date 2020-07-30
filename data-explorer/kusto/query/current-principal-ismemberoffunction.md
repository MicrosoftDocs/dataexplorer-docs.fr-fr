---
title: current_principal_is_member_of ()-Azure Explorateur de données
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
ms.openlocfilehash: 67d589f15a414ed715a75cc1871549b06f3dba9b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348664"
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

## <a name="syntax"></a>Syntaxe

`current_principal_is_member_of`(`*list of string literals*`)

## <a name="arguments"></a>Arguments

* *liste d’expressions* : liste de littéraux de chaîne séparés par des virgules, où chaque littéral est une chaîne de nom complet principal (FQN) formée comme suit :  
*PrinciplaType* `=` *PrincipalId* `;` *TenantId*

| PrincipalType   | Préfixe FQN  |
|-----------------|-------------|
| Utilisateur AAD        | `aaduser=`  |
| Groupe AAD       | `aadgroup=` |
| Application AAD | `aadapp=`   |

## <a name="returns"></a>Retourne
  
La fonction retourne :
* `true`: si le principal en cours d’exécution de la requête a été correctement mis en correspondance pour au moins un argument d’entrée.
* `false`: si le principal en cours d’exécution de la requête n’est membre d’aucun `aadgroup=` argument FQN et n’est pas égal à l’un des `aaduser=` `aadapp=` arguments ou FQN.
* `(null)`: si le principal en cours d’exécution de la requête n’est membre d’aucun `aadgroup=` argument FQN et n’est pas égal à l’un des `aaduser=` `aadapp=` arguments ou FQN, et qu’au moins un argument FQN n’a pas été résolu avec succès (n’a pas été enfoncé dans Azure AD). 

> [!NOTE]
> Étant donné que la fonction retourne une valeur à trois États ( `true` , `false` et `null` ), il est important de s’appuyer uniquement sur les valeurs de retour positives pour confirmer la réussite de l’appartenance. En d’autres termes, les expressions suivantes ne sont pas les mêmes :
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples -->
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

Utilisation d’un tableau dynamique au lieu de plusieurs arguments :

<!-- csl: https://help.kusto.windows.net/Samples -->
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
