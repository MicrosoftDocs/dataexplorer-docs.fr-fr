---
title: fonction .drop - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la fonction .drop dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8930f7333ff18fad0d5b3dbbebe9328f8bf7a0b9
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744776"
---
# <a name="drop-function"></a>.drop function

Laisse tomber une fonction de la base de données.
Pour laisser tomber plusieurs fonctions de la base de données, voir [.drop fonctions](#drop-functions).

**Syntaxe**

`.drop``function` *FunctionName* `ifexists`[ ]

* `ifexists`: Si spécifié, modifie le comportement de la commande de ne pas manquer pour une fonction inexistante.

> [!NOTE]
> * Nécessite [l’autorisation d’administration de base de données](../management/access-control/role-based-authorization.md).
> * [L’utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la fonction à l’origine est autorisé à modifier la fonction.  
    
|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Le nom de la fonction qui a été supprimée
 
**Exemple** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>.drop fonctions

Supprime plusieurs fonctions de la base de données.

**Syntaxe**

`.drop``functions` (*FunctionName1*, *FunctionName2*,..) [ifexists]

**Retourne**

Cette commande renvoie une liste des fonctions restantes dans la base de données.

|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Les paramètres requis par la fonction.
|body  |String |(Zéro ou plus) `let` déclarations suivies d’une expression CSL valide qui est évaluée sur l’invocation de la fonction.
|Dossier|String|Un dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne change pas la façon dont la fonction est invoquée.
|DocString (en)|String|Une description de la fonction à des fins d’assurance-chômage.

Nécessite [la permission d’administration de fonction](../management/access-control/role-based-authorization.md).

**Exemple** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
