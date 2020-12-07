---
title: . Create, fonction-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la fonction. Create dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f0a509e33ca1bc4c6d4a400428898b7c241222a2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320976"
---
# <a name="create-function"></a>.create function

Crée une fonction stockée, qui est une fonction d' [ `let` instruction](../query/letstatement.md) réutilisable avec le nom donné. La définition de fonction est rendue persistante avec les métadonnées de la base de données.

Les fonctions peuvent appeler d’autres fonctions (la récursif n’est pas prise en charge), et les `let` instructions sont autorisées dans le corps de la *fonction*. Consultez [ `let` instructions](../query/letstatement.md).

Les règles pour les types de paramètres et les instructions CSL sont les mêmes que pour les [ `let` instructions](../query/letstatement.md).
    
**Syntaxe**

`.create``function`[ `ifnotexists` ] [ `with` `(` [ `docstring` `=` *Documentation*] [ `,` `folder` `=` *NomDossier*] `)` ] [ `,` `skipvalidation` `=` 'true'] `)` ] *nomfonction* `(` *paramName* `:` *type ParamType* [ `,` ...] `)` `{` *FunctionBody*`}`

**Sortie**    
    
|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction. 
|Paramètres  |String |Paramètres requis par la fonction.
|Corps  |String |(Zéro, un ou plusieurs) `let` instructions suivies d’une expression CSL valide qui est évaluée lors de l’appel de la fonction.
|Dossier|String|Dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la manière dont la fonction est appelée.
|DocString|String|Description de la fonction pour les besoins de l’interface utilisateur.

> [!NOTE]
> * Si la fonction existe déjà :
>    * Si `ifnotexists` l’indicateur est spécifié, la commande est ignorée (aucune modification n’est appliquée).
>    * Si `ifnotexists` l’indicateur n’est pas spécifié, une erreur est retournée.
>    * Pour modifier une fonction existante, consultez. [`.alter function`](alter-function.md)
> * Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).
> * Tous les types de données ne sont pas pris en charge dans les `let` instructions. Les types pris en charge sont les suivants : Boolean, String, long, DateTime, TimeSpan, double et Dynamic.
> * Utilisez « SkipValidation » pour ignorer la validation sémantique de la fonction. Cela est utile lorsque les fonctions sont créées dans un ordre incorrect et que la touche F1 qui utilise F2 est créée précédemment.

**Exemples** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|Nom|Paramètres|Corps|Dossier|DocString|
|---|---|---|---|---|
|MyFunction1|()|{StormEvents &#124; limite 100}|Démonstration|Fonction de démonstration simple|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|Nom|Paramètres|Corps|Dossier|DocString|
|---|---|---|---|---|
|MyFunction2|(myLimit : long)|{StormEvents &#124; limite myLimit}|Démonstration|Fonction Demo avec un paramètre|
