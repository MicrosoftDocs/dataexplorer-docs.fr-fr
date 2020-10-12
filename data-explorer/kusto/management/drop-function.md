---
title: . Drop, fonction-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la fonction. Drop dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 279af228d15f511b35c26eebd0d21521450be786
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954737"
---
# <a name="drop-function"></a>.drop function

Supprime une fonction de la base de données.
Pour supprimer plusieurs fonctions de la base de données, consultez [. Drop Functions](#drop-functions).

**Syntaxe**

`.drop``function` *Nomfonction* [ `ifexists` ]

* `ifexists`: Si spécifié, modifie le comportement de la commande de sorte qu’il n’échoue pas pour une fonction inexistante.

> [!NOTE]
> * Requiert l' [autorisation Function admin](../management/access-control/role-based-authorization.md).
    
|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction qui a été supprimée.
 
**Exemple** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>. Drop, fonctions

Supprime plusieurs fonctions de la base de données.

**Syntaxe**

`.drop``functions`(*FunctionName1*, *FunctionName2*,..) [IfExists]

**Retourne**

Cette commande retourne une liste des fonctions restantes dans la base de données.

|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Paramètres requis par la fonction.
|body  |String |(Zéro, un ou plusieurs) `let` instructions suivies d’une expression CSL valide qui est évaluée lors de l’appel de la fonction.
|Dossier|String|Dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la façon dont la fonction est appelée.
|DocString|String|Description de la fonction pour les besoins de l’interface utilisateur.

Requiert l' [autorisation Function admin](../management/access-control/role-based-authorization.md).

**Exemple** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
