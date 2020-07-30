---
title: countof ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit countof () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d34b0611db134a6fc99daa49d04bfc19575a1c1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348749"
---
# <a name="countof"></a>countof()

Compte les occurrences d’une sous-chaîne dans une chaîne. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

## <a name="syntax"></a>Syntaxe

`countof(`*texte* `,` *Rechercher* [ `,` *genre*]`)`

## <a name="arguments"></a>Arguments

* *Text*: chaîne.
* *recherche*: chaîne simple ou [expression régulière](./re2.md) à faire correspondre à l’intérieur du *texte*.
* *genre*: `"normal"|"regex"` valeur par défaut `normal` . 

## <a name="returns"></a>Retours

Le nombre de fois où la chaîne de recherche peut être mise en correspondance dans le conteneur. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

## <a name="examples"></a>Exemples

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (pas 2 !)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    