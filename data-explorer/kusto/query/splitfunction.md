---
title: split() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la scission () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6e3935c524056afd27eb0d5e2d80925e072c8fa7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507461"
---
# <a name="split"></a>split()

Divise une chaîne donnée selon un délimitant donné et renvoie un tableau de cordes avec les sous-cordes contenues.

Éventuellement, une sous-chaîne spécifique peut être retournée si elle existe.

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

**Syntaxe**

`split(`*source* `,` *delimiter* [`,` *demandéIndex*]`)`

**Arguments**

* *source*: La chaîne source qui sera divisée selon le délimitant donné.
* *delimiter*: délimiteur utilisé pour fractionner la chaîne source.
* *requestedIndex* : index de base zéro facultatif `int`. S’il est fourni, le tableau de chaînes retourné contient la sous-chaîne demandée si elle existe. 

**Retourne**

Un tableau de chaînes qui contient les sous-chaînes de la chaîne source donnée séparées par le délimiteur donné.

**Exemples**

```kusto
print
    split("aa_bb", "_"),           // ["aa","bb"]
    split("aaa_bbb_ccc", "_", 1),  // ["bbb"]
    split("", "_"),                // [""]
    split("a__b", "_"),            // ["a","","b"]
    split("aabbcc", "bb")          // ["aa","cc"]
```