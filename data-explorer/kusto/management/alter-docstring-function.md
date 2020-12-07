---
title: . Alter, fonction DocString-Azure Explorateur de données
description: Cet article décrit la fonction. Alter Function DocString dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: e364a3cc5607b1a4b629954c93bf90e9173c00f1
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321758"
---
# <a name="alter-function-docstring"></a>.alter function docstring

Modifie la `DocString` valeur d’une fonction existante.

`.alter``function` *FunctionName* `docstring` *Documentation* nomfonction

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * L' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la fonction à l’origine est autorisé à modifier la fonction.
> * Si la fonction n’existe pas, une erreur est retournée. Pour plus d’informations sur la création d’une fonction, consultez [`.create function`](create-function.md) .

|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction
|Paramètres  |String |Paramètres requis par la fonction
|Corps  |String |(Zéro, un ou plusieurs) `let` instructions suivies d’une expression CSL valide qui est évaluée lorsque la fonction est appelée
|Dossier|String|Dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la façon dont la fonction est appelée
|DocString|String|Description de la fonction pour les besoins de l’interface utilisateur

**Exemple** 

```kusto
.alter function MyFunction1 docstring "Updated docstring"
```
    
|Nom |Paramètres |Corps|Dossier|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit : long)| {StormEvents &#124; limite myLimit}|Mondossier|Mise à jour DocString|
