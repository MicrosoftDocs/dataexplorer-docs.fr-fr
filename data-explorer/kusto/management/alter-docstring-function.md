---
title: .alter fonction docstring - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .alter fonction docstring dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25f6dac67f65add545f7215b44daafb0257a8375
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522574"
---
# <a name="alter-function-docstring"></a>.alter fonction docstring

Modifie la valeur DocString d’une fonction existante.

`.alter``function` *Documentation* *FunctionName* `docstring`

> [!NOTE]
> * Nécessite [l’autorisation d’administration de base de données](../management/access-control/role-based-authorization.md)
> * [L’utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la fonction à l’origine est autorisé à modifier la fonction. 
> * Si la fonction n’existe pas, une erreur est retournée. Pour créer une nouvelle fonction, voir [.créer la fonction](create-function.md)

|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Les paramètres requis par la fonction.
|body  |String |(Zéro ou plus) `let` déclarations suivies d’une expression CSL valide qui est évaluée sur l’invocation de la fonction.
|Dossier|String|Un dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne change pas la façon dont la fonction est invoquée.
|DocString (en)|String|Une description de la fonction à des fins d’assurance-chômage.

**Exemple** 

```
.alter function MyFunction1 docstring "Updated docstring"
```
    
|Nom |Paramètres |body|Dossier|DocString (en)
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| "StormEvents &#124; limite myLimit"|MyFolder (en)|Mise à jour docstring|