---
title: indexof() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit indexof() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 698cc7c13c3d665f9f5cfe25a31269dc763c51fb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513938"
---
# <a name="indexof"></a>indexof()

Fonction indique l’index zéro de la première occurrence d’une chaîne spécifiée dans la chaîne d’entrée.

Si la chaîne de recherche ou d’entrée n’est pas de type de chaîne - lance de force la valeur à la ficelle.

Voir [`indexof_regex()`](indexofregexfunction.md).

**Syntaxe**

`indexof(`*source*`,`*de recherche*`[,`*start_index*`[,`*occurrence* *de longueur*`[,``]]])`

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
 idx1 = indexof("abcdefg","cde")    // lookup found in input string
 , idx2 = indexof("abcdefg","cde",1,4) // lookup found in researched range 
 , idx3 = indexof("abcdefg","cde",1,2) // search starts from index 1, but stops after 2 chars, so full lookup can't be found
 , idx4 = indexof("abcdefg","cde",3,4) // search starts after occurrence of lookup
 , idx5 = indexof("abcdefg","cde",-1)  // invalid input
 , idx6 = indexof(1234567,5,1,4)       // two first parameters were forcibly casted to strings "12345" and "5"
 , idx7 = indexof("abcdefg","cde",2,-1)  // lookup found in input string
 , idx8 = indexof("abcdefgabcdefg", "cde", 1, 10, 2)   // lookup found in input range
 , idx9 = indexof("abcdefgabcdefg", "cde", 1, -1, 3)   // the third occurrence of lookup is not in researched range
```

|idx1 (idx1)|idx2 (idx2)|idx3 (idx3)|idx4 (idx4)|idx5 (idx5)|idx6 (en)|idx7 (idx7)|idx8 (en)|idx9 (en)|
|----|----|----|----|----|----|----|----|----|
|2   |2   |-1  |-1  |    |4   |2   |9   |-1  |
