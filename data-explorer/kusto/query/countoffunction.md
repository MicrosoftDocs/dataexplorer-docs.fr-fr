---
title: countof() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le compte de () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d932fbcea9b38849e7d7de09230c9a5aa9fa8e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516896"
---
# <a name="countof"></a>countof()

Compte les occurrences d’une sous-chaîne dans une chaîne. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

**Syntaxe**

`countof(`*text*`,` *recherche* de`,` texte [ *genre*]`)`

**Arguments**

* *texte*: Une chaîne.
* *recherche*: La chaîne simple ou [l’expression régulière](./re2.md) pour correspondre au *texte*intérieur.
* *type* `"normal"|"regex"` : `normal`Par défaut . 

**Retourne**

Le nombre de fois où la chaîne de recherche peut être mise en correspondance dans le conteneur. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

**Exemples**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (pas 2 !)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    