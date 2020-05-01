---
title: . ALTER FUNCTION, dossier-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le dossier. Alter Function dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 6becb5e29fd5771e1027c824b5a3539ed3c33b88
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617831"
---
# <a name="alter-function-folder"></a>. ALTER FUNCTION, dossier

Modifie la valeur de dossier d’une fonction existante.

`.alter``function` *FunctionName* `folder` *Dossier* nomfonction

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * L' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la fonction à l’origine est autorisé à modifier la fonction. 
> * Si la fonction n’existe pas, une erreur est retournée. Pour créer une fonction, [. Create, fonction](create-function.md)

|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Paramètres requis par la fonction.
|body  |String |(Zéro, un ou plusieurs) Instructions Let suivies d’une expression CSL valide qui est évaluée lors de l’appel de la fonction.
|Dossier|String|Dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la manière dont la fonction est appelée.
|DocString|String|Description de la fonction pour les besoins de l’interface utilisateur.

**Exemple** 

```kusto
.alter function MyFunction1 folder "Updated Folder"
```
    
|Nom |Paramètres |body|Dossier|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit : long)| {StormEvents &#124; limite myLimit}|Dossier mis à jour|Certains DocString|

```kusto
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|Nom |Paramètres |body|Dossier|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit : long)| {StormEvents &#124; limite myLimit}|Premier niveau Level\Second|Certains DocString|