---
title: Type de données String-Azure Explorateur de données
description: Cet article décrit le type de données String dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7c043a9b4d8b141d2dc45e88022e191e6483c35
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264808"
---
# <a name="the-string-data-type"></a>Type de données String

Le `string` type de données représente une chaîne Unicode. Les chaînes Kusto sont encodées au format UTF-8 et sont par défaut limitées à 1 Mo.

## <a name="string-literals"></a>Littéraux de chaîne

Il existe plusieurs façons d’encoder des littéraux du `string` type de données.

* Placez la chaîne entre guillemets doubles ( `"` ) :`"This is a string literal. Single quote characters (') don't require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* Placez la chaîne entre des guillemets simples ( `'` ) :`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

Dans les deux représentations ci-dessus, la barre oblique inverse ( `\` ) indique un caractère d’échappement.
Il est utilisé pour échapper les caractères de guillemet englobant, les caractères de tabulation ( `\t` ), les caractères de saut de ligne ( `\n` ) et lui-même ( `\\` ).

Les littéraux de chaîne Verbatim sont également pris en charge. Dans ce formulaire, la barre oblique inverse ( `\` ) correspond à elle-même, et non pas à un caractère d’échappement.

* Entourez les guillemets doubles ( `"` ) :`@"This is a verbatim string literal that ends with a backslash\"`
* Insérer des guillemets simples ( `'` ) :`@'This is a verbatim string literal that ends with a backslash\'`

Deux littéraux de chaîne dans la requête sans rien entre eux, ou séparés uniquement par un espace blanc et des commentaires, joignez automatiquement pour former un nouveau littéral de chaîne.
Par exemple, les expressions suivantes donnent toute la longueur `13` .

```kusto
print strlen("Hello"', '@"world!"); // Nothing between them

print strlen("Hello" ', ' @"world!"); // Separated by whitespace only

print strlen("Hello"
  // Comment
  ', '@"world!"); // Separated by whitespace and a comment
```

## <a name="examples"></a>Exemples

```kusto
// Simple string notation
print s1 = 'some string', s2 = "some other string"

// Strings that include single or double-quotes can be defined as follows
print s1 = 'string with " (double quotes)',
          s2 = "string with ' (single quotes)"

// Strings with '\' can be prefixed with '@' (as in c#)
print myPath1 = @'C:\Folder\filename.txt'

// Escaping using '\' notation
print s = '\\n.*(>|\'|=|\")[a-zA-Z0-9/+]{86}=='
```

Comme vous pouvez le constater, quand une chaîne est entourée de guillemets doubles ( `"` ), le caractère à guillemet simple ( `'` ) ne nécessite pas d’échappement, et d’autre part. Cette méthode facilite le devis des chaînes en fonction du contexte.

## <a name="obfuscated-string-literals"></a>Littéraux de chaîne obscurcis

Le système effectue le suivi des requêtes et les stocke à des fins de télémétrie et d’analyse.
Par exemple, le texte de la requête peut être mis à la disposition du propriétaire du cluster. Si le texte de la requête contient des informations secrètes, telles que des mots de passe, il peut y avoir des fuites d’informations qui doivent rester privées. Pour éviter qu’une telle fuite ne se produise, l’auteur de la requête peut marquer des littéraux de chaîne spécifiques comme des **littéraux de chaîne obscurcis**.
Ces littéraux dans le texte de la requête sont automatiquement remplacés par un nombre de `*` caractères étoile (), afin qu’ils ne soient pas disponibles pour une analyse ultérieure.

> [!IMPORTANT]
> Marquez tous les littéraux de chaîne qui contiennent des informations secrètes comme littéraux de chaîne obscurcis.

Un littéral de chaîne obscurci peut être formé en acceptant un littéral de chaîne « normal » et en ajoutant un `h` caractère ou un `H` caractère devant celui-ci. 

Par exemple :

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> Dans de nombreux cas, seule une partie du littéral de chaîne est secrète. Dans ce cas, fractionnez le littéral en un composant non secret et un composant secret. Ensuite, marquez uniquement la partie secrète comme obscurcie.

Par exemple :

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```
