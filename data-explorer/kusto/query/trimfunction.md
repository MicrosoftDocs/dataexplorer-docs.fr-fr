---
title: Trim ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Trim () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a5a8bf4884bf6c493c1b3b960fce64fe143ed52e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251903"
---
# <a name="trim"></a>trim()

Supprime toutes les correspondances de début et de fin de l’expression régulière spécifiée.

## <a name="syntax"></a>Syntaxe

`trim(`*expression régulière* `,` *texte*`)`

## <a name="arguments"></a>Arguments

* *Regex*: chaîne ou [expression régulière](re2.md) à tronquer à partir du début et/ou de la fin du *texte*.  
* *Text*: chaîne.

## <a name="returns"></a>Retours

*texte* après le rognage des correspondances de *Regex* trouvées au début et/ou à la fin du *texte*.

## <a name="example"></a>Exemple

L’instruction découpe la *sous-chaîne*  à partir du début et de la fin de l' *string_to_trim*:

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

L’instruction suivante supprime tous les caractères non alphabétiques du début et de la fin de la chaîne :

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|-Te ST1//$|Te-ST1|
|-Te ST2//$|Te ST2|
|-Te ST3//$|Te ST3|
|-Te ST4//$|Te ST4|
|-Te ST5//$|Te ST5|


 