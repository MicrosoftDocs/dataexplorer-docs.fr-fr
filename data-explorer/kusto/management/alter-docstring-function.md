---
title: . Alter Function DocString-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la fonction. Alter Function DocString dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d590e93a6772483aba6b9580b26490eb2fe5ec08
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617848"
---
# <a name="alter-function-docstring"></a>. Alter, fonction DocString

Modifie la valeur DocString d’une fonction existante.

`.alter``function` *FunctionName* `docstring` *Documentation* nomfonction

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * L' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la fonction à l’origine est autorisé à modifier la fonction. 
> * Si la fonction n’existe pas, une erreur est retournée. Pour créer une fonction, consultez [. Create, fonction](create-function.md)

|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Paramètres requis par la fonction.
|body  |String |(Zéro, un ou plusieurs) `let` instructions suivies d’une expression CSL valide qui est évaluée lors de l’appel de la fonction.
|Dossier|String|Dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la façon dont la fonction est appelée.
|DocString|String|Description de la fonction pour les besoins de l’interface utilisateur.

**Exemple** 

```kusto
.alter function MyFunction1 docstring "Updated docstring"
```
    
|Nom |Paramètres |body|Dossier|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit : long)| {StormEvents &#124; limite myLimit}|Mondossier|Mise à jour DocString|