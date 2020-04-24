---
title: trim() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la garniture () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aef2a61e5ac13fe9af9d8bc0dd130f3d085a3604
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505591"
---
# <a name="trim"></a>trim()

Supprime tous les matchs de tête et de fuite de l’expression régulière spécifiée.

**Syntaxe**

`trim(`*texte regex* `,` *text*`)`

**Arguments**

* *regex*: Chaîne ou [expression régulière](re2.md) à couper dès le début et/ou la fin du *texte*.  
* *texte*: Une chaîne.

**Retourne**

*texte* après la coupe des allumettes de *regex* trouvé au début et / ou la fin du *texte*.

**Exemple**

Déclaration soufflet garnitures *sous-cordes* depuis le début et la fin de la *string_to_trim*:

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

La déclaration suivante coupe tous les caractères non-mots du début et de la fin de la chaîne :

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|- Te st1// $|Te st1|
|- Te st2// $|Te st2|
|- Te st3// $|Te st3|
|- Te st4// $|Te st4|
|- Te st5// $|Te st5|


 