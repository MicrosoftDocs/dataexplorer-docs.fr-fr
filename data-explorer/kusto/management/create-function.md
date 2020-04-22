---
title: .créer la fonction - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .créer la fonction dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bc2caa5dcc886964b42bf1915bf3a003f2d32c7a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744461"
---
# <a name="create-function"></a>.create function

Crée une fonction stockée, qui est une fonction [ `let` d’instruction](../query/letstatement.md) réutilisable avec le nom donné. La définition de fonction persiste avec les métadonnées de base de données.

Les fonctions peuvent appeler d’autres fonctions (la récursivité n’est pas prise en charge), et `let` les déclarations sont autorisées dans le cadre du corps de *fonction*. Voir [ `let` les déclarations](../query/letstatement.md).

Les règles relatives aux types de paramètres et aux énoncés CSL sont les mêmes que pour [ `let` les relevés](../query/letstatement.md).
    
**Syntaxe**

`.create``function` [`ifnotexists`]`with` `(``docstring` `=` [ *Documentation*] [`,` `folder` `=` `)` *FolderName*] `)`] [`,` `skipvalidation` `=` 'true'] ] *FunctionName* `(` *ParamName* `:` *ParamType* [`,` ...] `)` `{` *FonctionBody*`}`

**Sortie**    
    
|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Les paramètres requis par la fonction.
|body  |String |(Zéro ou plus) `let` déclarations suivies d’une expression CSL valide qui est évaluée sur l’invocation de la fonction.
|Dossier|String|Un dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne change pas la façon dont la fonction est invoquée.
|DocString (en)|String|Une description de la fonction à des fins d’assurance-chômage.

> [!NOTE]
> * Si la fonction existe déjà :
>    * Si `ifnotexists` le drapeau est spécifié, la commande est ignorée (aucun changement appliqué).
>    * Si `ifnotexists` le drapeau n’est PAS spécifié, une erreur est retournée.
>    * Pour modifier une fonction existante, voir [fonction .alter](alter-function.md)
> * Nécessite la [permission de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).
> * Tous les types de `let` données ne sont pas pris en charge dans les déclarations. Les types pris en charge sont : boolean, string, long, date, timepan, double, et dynamique.
> * Utilisez la 'skipvalidation' pour sauter la validation sémantique de la fonction. Ceci est utile lorsque les fonctions sont créées dans un ordre incorrect et F1 qui utilise F2 est créé plus tôt.

**Exemples** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|Nom|Paramètres|body|Dossier|DocString (en)|
|---|---|---|---|---|
|MyFunction1 (en)|()|"StormEvents &#124; limite 100"|Démonstration|Fonction de démonstration simple|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|Nom|Paramètres|body|Dossier|DocString (en)|
|---|---|---|---|---|
|MyFunction2|(myLimit:long)|"StormEvents &#124; limite myLimit"|Démonstration|Fonction de démonstration avec paramètre|
