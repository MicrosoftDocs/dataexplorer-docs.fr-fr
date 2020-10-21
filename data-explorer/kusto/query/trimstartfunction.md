---
title: trim_start ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit trim_start () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f4341e984504c89bfc4d5a1265c5193ac6d0297
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251884"
---
# <a name="trim_start"></a>trim_start()

Supprime la correspondance de début de l’expression régulière spécifiée.

## <a name="syntax"></a>Syntaxe

`trim_start(`*expression régulière* `,` *texte*`)`

## <a name="arguments"></a>Arguments

* *Regex*: chaîne ou [expression régulière](re2.md) à tronquer à partir du début du *texte*.  
* *Text*: chaîne.

## <a name="returns"></a>Retours

*texte* après troncation de la correspondance d' *expression régulière* trouvée au début du *texte*.

## <a name="example"></a>Exemple

L’instruction « souffle » supprime la *sous-chaîne*  à partir du début de *string_to_trim*:

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

L’instruction suivante supprime tous les caractères non alphabétiques à partir du début de la chaîne :

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|-Te ST1//$|Te ST1//$|
|-Te ST2//$|Te ST2//$|
|-Te ST3//$|Te ST3//$|
|-Te ST4//$|Te ST4//$|
|-Te ST5//$|Te ST5//$|

 