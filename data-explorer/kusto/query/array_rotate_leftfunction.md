---
title: array_rotate_left ()-Azure Explorateur de données
description: Cet article décrit array_rotate_left () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 7c9e6a318ebcd0a0911b3a86c1bf0ad99aeb2f51
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248114"
---
# <a name="array_rotate_left"></a>array_rotate_left()

Fait pivoter les valeurs à l’intérieur `dynamic` d’un tableau vers la gauche.

## <a name="syntax"></a>Syntaxe

`array_rotate_left(`*arr*, *rotate_count*`)`

## <a name="arguments"></a>Arguments

* *arr*: tableau d’entrée à fractionner, doit être un tableau dynamique.
* *rotate_count*: entier spécifiant le nombre de positions que les éléments de tableau feront pivoter vers la gauche. Si la valeur est négative, les éléments sont pivotés vers la droite.

## <a name="returns"></a>Retours

Tableau dynamique contenant la même quantité d’éléments que dans le tableau d’origine, où chaque élément a été pivoté d’après *rotate_count*.

## <a name="see-also"></a>Voir aussi

* Pour faire pivoter un tableau vers la droite, consultez [array_rotate_right ()](array_rotate_rightfunction.md).
* Pour décaler le tableau vers la gauche, consultez [array_shift_left ()](array_shift_leftfunction.md).
* Pour décaler le tableau vers la droite, consultez [array_shift_right ()](array_shift_rightfunction.md).

## <a name="examples"></a>Exemples

* Rotation à gauche de deux positions :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, 2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5, 1, 2]|

* Faire pivoter vers la droite de deux positions à l’aide d’une valeur rotate_count négative :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, -2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[4, 5, 1, 2, 3]|