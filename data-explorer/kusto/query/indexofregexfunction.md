---
title: indexof_regex() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit indexof_regex() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6da0523e85bab4883c50708ffe3f7d087fdd8c8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513887"
---
# <a name="indexof_regex"></a>indexof_regex()

Fonction indique l’index zéro de la première occurrence d’une chaîne spécifiée dans la chaîne d’entrée. Les allumettes à cordes simples ne se chevauchent pas. 

Voir [`indexof()`](indexoffunction.md).

**Syntaxe**

`indexof_regex(`*source*`,`*de recherche*`[,`*start_index*`[,`*occurrence* *de longueur*`[,``]]])`

**Arguments**

* *source*: chaîne d’entrée.  
* *lookup*: corde à rechercher.
* *start_index*: poste de départ de recherche (facultatif).
* *longueur*: nombre de positions de caractère à examiner, -1 définissant la longueur illimitée (facultatif).
* *:* est l’occurrence Par défaut 1 (facultatif).

**Retourne**

Position de l’indice zéro de la *recherche*.

Retourne -1 si la chaîne ne se trouve pas dans l’entrée.
En cas de paramètres de *longueur* non pertinent (moins de 0) *start_index,* *occurrence* ou (moins de -1) - retours *nuls*.


**Exemples**
```kusto
print
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1 (idx1)|idx2 (idx2)|idx3 (idx3)|idx4 (idx4)|idx5 (idx5)
|----|----|----|----|----
|0   |3   |-1  |-1  |    