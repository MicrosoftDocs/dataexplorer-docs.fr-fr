---
title: trim_end() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit trim_end () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a6f6ffc264cb436fc61d74f08dfded915caa05d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505642"
---
# <a name="trim_end"></a>trim_end()

Supprime la correspondance de fuite de l’expression régulière spécifiée.

**Syntaxe**

`trim_end(`*texte regex* `,` *text*`)`

**Arguments**

* *regex*: Chaîne ou [expression régulière](re2.md) à couper à partir de la fin du *texte*.  
* *texte*: Une chaîne.

**Retourne**

*texte* après la coupe des allumettes de *regex* trouvé à la fin du *texte*.

**Exemple**

Déclaration de soufflet garnitures *sous-cordes* de la fin de *string_to_trim*:

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |bing          |

La déclaration suivante coupe tous les caractères non-mots de la fin de la chaîne :

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|str          |trimmed_str|
|-------------|-----------|
|- Te st1// $|- Te st1  |
|- Te st2// $|- Te st2  |
|- Te st3// $|- Te st3  |
|- Te st4// $|- Te st4  |
|- Te st5// $|- Te st5  |