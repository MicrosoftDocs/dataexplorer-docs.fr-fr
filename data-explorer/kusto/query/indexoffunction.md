---
title: IndexOf ()-Explorateur de données Azure
description: Cet article décrit IndexOf () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8e237441d28f12ffc6f27f8a591980a701825e39
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347457"
---
# <a name="indexof"></a>indexof()

Signale l’index de base zéro de la première occurrence d’une chaîne spécifiée dans la chaîne d’entrée.

Si la recherche ou la chaîne d’entrée n’est pas de type *chaîne* , la fonction effectue un cast forcé de la valeur en *chaîne*.

Pour plus d’informations, consultez [`indexof_regex()`](indexofregexfunction.md).

## <a name="syntax"></a>Syntaxe

`indexof(`*source* `,` *recherche* `[,` *start_index* `[,` *longueur* `[,` *occurrence*`]]])`

## <a name="arguments"></a>Arguments

* *source*: chaîne d’entrée.  
* *Lookup*: chaîne à rechercher.
* *start_index*: position de début de la recherche. facultatif.
* *longueur*: nombre de positions de caractère à examiner. La valeur-1 signifie une longueur illimitée. facultatif.
* *occurrence*: numéro de l’occurrence. Valeur par défaut : 1. facultatif.

## <a name="returns"></a>Retours

Position d’index de base zéro de la *recherche*.

Retourne-1 si la chaîne est introuvable dans l’entrée.

Si *ce paramètre n'* est pas pertinent (inférieur à 0) *start_index*, *occurrence*ou (inférieur à-1), retourne la *valeur null*.

## <a name="examples"></a>Exemples
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

|idx1|idx2|idx3|idx4|idx5|idx6|idx7|idx8|idx9|
|----|----|----|----|----|----|----|----|----|
|2   |2   |-1  |-1  |    |4   |2   |9   |-1  |
