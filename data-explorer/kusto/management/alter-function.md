---
title: . Alter, fonction-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la fonction. Alter dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: fc7c29542f63ea9b659bd3318d1442d9bca4ae48
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321707"
---
# <a name="alter-function"></a>.alter function

Modifie une fonction existante et la stocke dans les métadonnées de la base de données.
Les règles pour les types de paramètres et les instructions CSL sont les mêmes que pour les [ `let` instructions](../query/letstatement.md).

**Syntaxe**

```kusto
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|Paramètre de sortie |Type |Description
|---|---|--- 
|Nom  |String |Nom de la fonction.
|Paramètres  |String |Paramètres requis par la fonction.
|Corps  |String |(Zéro, un ou plusieurs) `let` instructions suivies d’une expression CSL valide qui est évaluée lors de l’appel de la fonction.
|Dossier|String|Dossier utilisé pour la catégorisation des fonctions d’interface utilisateur. Ce paramètre ne modifie pas la manière dont la fonction est appelée.
|DocString|String|Description de la fonction pour les besoins de l’interface utilisateur.

> [!NOTE]
> * Si la fonction n’existe pas, une erreur est retournée. Pour créer une nouvelle fonction, consultez. [`.create function`](create-function.md)
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * L' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la fonction à l’origine est autorisé à modifier la fonction. 
> * Tous les types Kusto ne sont pas pris en charge dans les `let` instructions. Les types pris en charge sont les suivants : String, long, DateTime, TimeSpan et double.
> * Utilisez `skipvalidation` pour ignorer la validation sémantique de la fonction. Cela est utile lorsque les fonctions sont créées dans un ordre incorrect et que la touche F1 qui utilise F2 est créée précédemment.
 
**Exemple** 

```kusto
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|Nom |Paramètres |Corps|Dossier|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit : long)| {StormEvents &#124; limite myLimit}|Mondossier|Fonction Demo avec un paramètre|
