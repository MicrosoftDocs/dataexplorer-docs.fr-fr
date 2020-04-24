---
title: .alter fonction - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la fonction .alter dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 3fb7b3aab9d140d5f3660ef1384bf54efa9cd825
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522455"
---
# <a name="alter-function"></a>.modifier la fonction

Modifie une fonction existante et la stocke à l’intérieur des métadonnées de base de données.
Les règles relatives aux types de paramètres et aux énoncés CSL sont les mêmes que pour [ `let` les relevés](../query/letstatement.md).

**Syntaxe**

```
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction.
|Paramètres  |String |Les paramètres requis par la fonction.
|body  |String |(Zéro ou plus) `let` déclarations suivies d’une expression CSL valide qui est évaluée sur l’invocation de la fonction.
|Dossier|String|Un dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la façon dont la fonction est invoquée.
|DocString (en)|String|Une description de la fonction à des fins d’assurance-chômage.

> [!NOTE]
> * Si la fonction n’existe pas, une erreur est retournée. Pour créer une nouvelle fonction, voir [.créer la fonction](create-function.md)
> * Nécessite [l’autorisation d’administration de base de données](../management/access-control/role-based-authorization.md)
> * [L’utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la fonction à l’origine est autorisé à modifier la fonction. 
> * Tous les types de Kusto ne sont pas pris en charge dans `let` les déclarations. Les types pris en charge sont : chaîne, longue, date, timepan, et double.
> * Utiliser `skipvalidation` pour sauter la validation sémantique de la fonction. Ceci est utile lorsque les fonctions sont créées dans un ordre incorrect et F1 qui utilise F2 est créé plus tôt.
 
**Exemple** 

```
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|Nom |Paramètres |body|Dossier|DocString (en)
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| "StormEvents &#124; limite myLimit"|MyFolder (en)|Fonction de démonstration avec paramètre|