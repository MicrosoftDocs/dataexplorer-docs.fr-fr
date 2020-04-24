---
title: trim_start() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit trim_start() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d4ae71f73e76005f89766d974192c8eb24cd74d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505574"
---
# <a name="trim_start"></a>trim_start()

Supprime le match de tête de l’expression régulière spécifiée.

**Syntaxe**

`trim_start(`*texte regex* `,` *text*`)`

**Arguments**

* *regex*: Chaîne ou [expression régulière](re2.md) à couper dès le début du *texte*.  
* *texte*: Une chaîne.

**Retourne**

*texte* après la coupe match de *regex* trouvé au début du *texte*.

**Exemple**

Déclaration soufflet garnitures *sous-cordes* dès le début de *string_to_trim*:

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

La déclaration suivante coupe tous les caractères non-mots dès le début de la chaîne :

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|- Te st1// $|Te st1// $|
|- Te st2// $|Te st2// $|
|- Te st3// $|Te st3// $|
|- Te st4// $|Te st4// $|
|- Te st5// $|Te st5// $|

 