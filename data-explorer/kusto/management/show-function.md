---
title: . Show, fonctions-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. afficher les fonctions dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aa81df5ca89c1a7dc523d7799050b8a7ad3adaba
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617326"
---
# <a name="show-functions"></a>. Show, fonctions

Répertorie toutes les fonctions stockées dans la base de données actuellement sélectionnée.
Pour retourner une seule fonction spécifique, consultez [. Show, fonction](#show-function).

```kusto
.show functions
```

Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).
 
|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Paramètres requis par la fonction.
|body  |String |(Zéro, un ou plusieurs) `let` instructions suivies d’une expression CSL valide qui est évaluée lors de l’appel de la fonction.
|Dossier|String|Dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la façon dont la fonction est appelée.
|DocString|String|Description de la fonction pour les besoins de l’interface utilisateur.
 
**Exemple de sortie** 

|Nom |Paramètres|body|Dossier|DocString
|---|---|---|---|---
|MyFunction1 |() | {StormEvents &#124; limite 100}|Mondossier|Fonction de démonstration simple|
|MyFunction2 |(myLimit : long)| {StormEvents &#124; limite myLimit}|Mondossier|Fonction Demo avec un paramètre|
|MyFunction3 |() | {StormEvents (100)}|Mondossier|Fonction appelant une autre fonction||

## <a name="show-function"></a>.show function

```kusto
.show function MyFunc1
```

Répertorie les détails d’une fonction stockée spécifique. Pour obtenir la liste de **toutes les** fonctions, consultez [. Show Functions](#show-functions).

**Syntaxe**

`.show``function` *Nomfonction*

**Sortie**

|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Paramètres requis par la fonction.
|body  |String |(Zéro, un ou plusieurs) `let` instructions suivies d’une expression CSL valide qui est évaluée lors de l’appel de la fonction.
|Dossier|String|Dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la manière dont la fonction est appelée.
|DocString|String|Description de la fonction pour les besoins de l’interface utilisateur.
 
> [!NOTE] 
> * Si la fonction n’existe pas, une erreur est retournée.
> * Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).
 
**Exemple** 

```kusto
.show function MyFunction1 
```
    
|Nom |Paramètres |body|Dossier|DocString
|---|---|---|---|---
|MyFunction1 |() | {StormEvents &#124; limite 100}|Mondossier|Fonction de démonstration simple
