---
title: .show fonctions - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .show fonctions dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7acfdb96570b067b0461f5a788b55fc8274c03f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519837"
---
# <a name="show-functions"></a>.afficher les fonctions

Répertorie toutes les fonctions stockées dans la base de données actuellement sélectionnée.
Pour retourner une seule fonction spécifique, voir [fonction .show](#show-function).

```
.show functions
```

Nécessite la [permission de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).
 
|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Les paramètres requis par la fonction.
|body  |String |(Zéro ou plus) `let` déclarations suivies d’une expression CSL valide qui est évaluée sur l’invocation de la fonction.
|Dossier|String|Un dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne change pas la façon dont la fonction est invoquée.
|DocString (en)|String|Une description de la fonction à des fins d’assurance-chômage.
 
**Exemple de sortie** 

|Nom |Paramètres|body|Dossier|DocString (en)
|---|---|---|---|---
|MyFunction1 (en) |() | "StormEvents &#124; limite 100"|MyFolder (en)|Fonction de démonstration simple|
|MyFunction2 |(myLimit: long)| "StormEvents &#124; limite myLimit"|MyFolder (en)|Fonction de démonstration avec paramètre|
|MyFunction3 |() | TempêteEvents(100)|MyFolder (en)|Fonction appelant d’autres fonctions||

## <a name="show-function"></a>.fonction de spectacle

```
.show function MyFunc1
```

Répertorie les détails d’une fonction stockée spécifique. Pour une liste de **toutes les** fonctions, voir [.show fonctions](#show-functions).

**Syntaxe**

`.show``function` *FunctionName (en)*

**Sortie**

|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Les paramètres requis par la fonction.
|body  |String |(Zéro ou plus) `let` déclarations suivies d’une expression CSL valide qui est évaluée sur l’invocation de la fonction.
|Dossier|String|Un dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne change pas la façon dont la fonction est invoquée
|DocString (en)|String|Une description de la fonction à des fins d’assurance-chômage.
 
> [!NOTE] 
> * Si la fonction n’existe pas, une erreur est retournée.
> * Nécessite la [permission de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).
 
**Exemple** 

```
.show function MyFunction1 
```
    
|Nom |Paramètres |body|Dossier|DocString (en)
|---|---|---|---|---
|MyFunction1 (en) |() | "StormEvents &#124; limite 100"|MyFolder (en)|Fonction de démonstration simple