---
title: Le type de données de chaîne - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le type de données de chaîne dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c040045ba7146cf56487ca3a8729372084d3bf2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509620"
---
# <a name="the-string-data-type"></a>Le type de données de chaîne

Le `string` type de données représente une chaîne Unicode. (Les cordes Kusto sont codées dans UTF-8 et par défaut sont limitées à 1 Mo.)

## <a name="string-literals"></a>Littéraux de chaîne

Il existe plusieurs façons d’encoder les littérals du `string` type de données :

* En enfermant la chaîne en`"`double-citations ( ):`"This is a string literal. Single quote characters (') do not require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* En enfermant la chaîne en`'`une seule citation ( ):`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

Dans les deux représentations ci-dessus, le caractère de barre oblique inverse (`\`) indique s’échapper.
Il est utilisé pour échapper aux caractères`\t`de citation,`\n`caractères`\\`onglet ( ), personnages newline ( ), et lui-même ( ).

Les littérals de cordes Verbatim sont également pris en charge. Sous cette forme, le personnage`\`de barre oblique inverse ( ) se tient pour lui-même, pas comme un personnage d’évasion:

* Enfermé dans des doubles-citations (`"`):`@"This is a verbatim string literal that ends with a backslash\"`
* Enfermés en une`'`seule citation ( ):`@'This is a verbatim string literal that ends with a backslash\'`

Deux littérals de chaîne dans le texte de requête avec rien entre eux, ou séparés seulement par l’espace blanc et les commentaires, sont automatiquement concatenated ensemble pour former une nouvelle chaîne littérale (jusqu’à ce qu’une telle substitution ne puisse pas être faite).
Par exemple, les expressions `13`suivantes donnent tous les rendements :

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

Comme on peut le voir, quand une chaîne`"`est enfermée`'`dans des doubles citations ( ), le personnage de citation unique ( ) ne nécessite pas de s’échapper et vice-versa. Il est ainsi plus facile de citer des chaînes selon le contexte.

## <a name="obfuscated-string-literals"></a>Littérals de cordes obfusques

Le système suit les requêtes et les stocke à des fins de télémétrie et d’analyse.
Par exemple, le texte de requête peut être mis à la disposition du propriétaire du cluster. Si le texte de requête comprend des informations secrètes (comme les mots de passe), cela peut divulguer des informations qui doivent être gardées privées. Pour éviter que cela ne se produise, l’auteur de requête peut marquer des littérals de cordes spécifiques comme **des littérals de cordes obscurcis.**
Ces littérals dans le texte de requête sont`*`automatiquement remplacés par un certain nombre de caractères d’étoile, de sorte qu’ils ne sont pas disponibles pour une analyse ultérieure.

> [!IMPORTANT]
> Il est **fortement recommandé** que tous les littérals de chaîne qui contiennent des informations secrètes soient marqués comme des littérals obscurcis de chaîne.

Une chaîne obfusquée littérale peut être formée en prenant une chaîne `h` "régulière" littérale, et en pré-dépenses d’un ou d’un `H` personnage en face de lui. Par exemple :

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> Dans de nombreux cas, seule une partie de la chaîne littérale est secrète. Il est très utile dans ces cas de diviser le littéral en une partie non secrète et une partie secrète, puis seulement marquer la partie secrète comme obscurci. Par exemple :

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```