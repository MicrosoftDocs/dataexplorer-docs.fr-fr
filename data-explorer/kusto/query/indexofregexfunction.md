---
title: indexof_regex ()-Azure Explorateur de données
description: Cet article décrit indexof_regex () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 85c39128eeb9b6ded38366ccd3bea228820c67a7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347440"
---
# <a name="indexof_regex"></a>indexof_regex()

La fonction signale l’index de base zéro de la première occurrence d’une chaîne spécifiée dans la chaîne d’entrée. Les correspondances de chaînes brutes ne se chevauchent pas.

Voir [`indexof()`](indexoffunction.md).

## <a name="syntax"></a>Syntaxe

`indexof_regex(`*source* `,` *recherche* `[,` *start_index* `[,` *longueur* `[,` *occurrence*`]]])`

## <a name="arguments"></a>Arguments

|Arguments     | Description                                     |Obligatoire ou facultatif|
|--------------|-------------------------------------------------|--------------------|
|source        | Chaîne d’entrée                                    |Obligatoire            |
|recherche        | Chaîne à rechercher                                  |Obligatoire            |
|start_index   | Position de début de la recherche                           |Facultatif            |
|length        | Nombre de positions de caractère à examiner. -1 définit une longueur illimitée |Facultatif            |
|occurrence    | Recherche l’index de la N-ième apparence du modèle. 
                 La valeur par défaut est 1, l’index de la première occurrence |Facultatif            |

## <a name="returns"></a>Retourne

Position d’index de base zéro de la *recherche*.

* Retourne-1 si la chaîne est introuvable dans l’entrée.
* Retourne *null* si :
     * start_index est inférieur à 0.
     * l’occurrence est inférieure à 0.
     * le paramètre de longueur est inférieur à-1.


## <a name="examples"></a>Exemples

```kusto
print
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
