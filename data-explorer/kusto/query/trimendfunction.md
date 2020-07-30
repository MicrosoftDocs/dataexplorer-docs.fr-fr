---
title: trim_end ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit trim_end () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cab78680a3b996234724bc052d75959928520289
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339848"
---
# <a name="trim_end"></a>trim_end()

Supprime la correspondance de fin de l’expression régulière spécifiée.

## <a name="syntax"></a>Syntaxe

`trim_end(`*expression régulière* `,` *texte*`)`

## <a name="arguments"></a>Arguments

* *Regex*: chaîne ou [expression régulière](re2.md) à tronquer à partir de la fin du *texte*.  
* *Text*: chaîne.

## <a name="returns"></a>Retourne

*texte* après le rognage des correspondances de l' *expression régulière* trouvée à la fin du *texte*.

## <a name="example"></a>Exemple

L’instruction « souffle » supprime la *sous-chaîne* à partir de la fin de *string_to_trim*:

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |bing          |

L’instruction Next supprime tous les caractères non alphabétiques à partir de la fin de la chaîne :

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|str          |trimmed_str|
|-------------|-----------|
|-Te ST1//$|-Te ST1  |
|-Te ST2//$|-Te ST2  |
|-Te ST3//$|-Te ST3  |
|-Te ST4//$|-Te ST4  |
|-Te ST5//$|-Te ST5  |