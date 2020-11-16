---
title: indexof_regex ()-Azure Explorateur de données
description: Cet article décrit indexof_regex () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5826920a411235166002b6833ed88869fb85f211
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638969"
---
# <a name="indexof_regex"></a>indexof_regex()

Retourne l’index de base zéro de la première occurrence d’une expression régulière de recherche spécifiée dans la chaîne d’entrée.

Voir [`indexof()`](indexoffunction.md).

## <a name="syntax"></a>Syntax

`indexof_regex(`*source* `,` *recherche* `[,` *start_index* `[,` *longueur* `[,` *occurrence*`]]])`

## <a name="arguments"></a>Arguments

|Arguments     | Description                                     |Obligatoire ou facultatif|
|--------------|-------------------------------------------------|--------------------|
|source        | Chaîne d’entrée                                    |Obligatoire            |
|recherche        | Chaîne de recherche d’expression régulière.               |Obligatoire            |
|start_index   | Position de début de la recherche                           |Facultatif            |
|length        | Nombre de positions de caractère à examiner. -1 définit une longueur illimitée |Facultatif            |
|occurrence    | Recherche l’index de la N-ième apparence du modèle. 
                 La valeur par défaut est 1, l’index de la première occurrence |Facultatif            |

## <a name="returns"></a>Retours

Position d’index de base zéro de la *recherche*.

* Retourne-1 si la chaîne est introuvable dans l’entrée.
* Retourne *null* si :
     * start_index est inférieur à 0.
     * l’occurrence est inférieure à 0.
     * le paramètre de longueur est inférieur à-1.

> [!NOTE]
- Les correspondances qui se chevauchent ne sont pas prises en charge.
- Les chaînes d’expression régulière peuvent contenir des caractères qui requièrent l’échappement ou l’utilisation de littéraux de chaîne @ ' '.

## <a name="examples"></a>Exemples

```kusto
print
 idx1 = indexof_regex("abcabc", @"a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", @"a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", @"a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", @"a.a", 0, -1, 2)  // Matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", @"a|ab", -1)  // invalid start_index argument
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
