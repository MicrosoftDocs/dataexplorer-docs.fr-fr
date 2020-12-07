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
ms.openlocfilehash: 37cedb7ba6e58f5b434101cd23b876cf18c2b78c
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321724"
---
# <a name="alter-function-folder"></a>.alter function folder

Modifie la valeur de dossier d’une fonction existante.

`.alter``function` *FunctionName* `folder` *Dossier* nomfonction

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * L' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la fonction à l’origine est autorisé à modifier la fonction. 
> * Si la fonction n’existe pas, une erreur est retournée. Pour créer une nouvelle fonction, [`.create function`](create-function.md)

|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Paramètres requis par la fonction.
|Corps  |String |(Zéro, un ou plusieurs) Instructions Let suivies d’une expression CSL valide qui est évaluée lors de l’appel de la fonction.
|Dossier|String|Dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la manière dont la fonction est appelée.
|DocString|String|Description de la fonction pour les besoins de l’interface utilisateur.

**Exemple** 

```kusto
.alter function MyFunction1 folder "Updated Folder"
```
    
|Nom |Paramètres |Corps|Dossier|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit : long)| {StormEvents &#124; limite myLimit}|Dossier mis à jour|Certains DocString|

```kusto
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|Nom |Paramètres |Corps|Dossier|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit : long)| {StormEvents &#124; limite myLimit}|Premier niveau Level\Second|Certains DocString|