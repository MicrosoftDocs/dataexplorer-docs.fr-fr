---
title: .alter fiche de fonction - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le dossier de fonction .alter dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 140991c723ebdd12fa17000ea845adbbfdd27771
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522540"
---
# <a name="alter-function-folder"></a>.modifier le dossier de fonction

Modifie la valeur Folder d’une fonction existante.

`.alter``function` *Dossier FunctionName* `folder` *Folder*

> [!NOTE]
> * Nécessite [l’autorisation d’administration de base de données](../management/access-control/role-based-authorization.md)
> * [L’utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la fonction à l’origine est autorisé à modifier la fonction. 
> * Si la fonction n’existe pas, une erreur est retournée. Pour créer une nouvelle fonction, [.créer la fonction](create-function.md)

|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Les paramètres qui sont requis par la fonction.
|body  |String |(Zéro ou plus) Laissez les déclarations suivies d’une expression CSL valide qui est évaluée sur l’invocation de la fonction.
|Dossier|String|Un dossier qui est utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la façon dont la fonction est invoquée.
|DocString (en)|String|Une description de la fonction à des fins d’assurance-chômage.

**Exemple** 

```
.alter function MyFunction1 folder "Updated Folder"
```
    
|Nom |Paramètres |body|Dossier|DocString (en)
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| "StormEvents &#124; limite myLimit"|Dossier mis à jour|Certains DocString|

```
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|Nom |Paramètres |body|Dossier|DocString (en)
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| "StormEvents &#124; limite myLimit"|Premier niveau-deuxième niveau|Certains DocString|