---
title: array_rotate_right ()-Azure Explorateur de données
description: Cet article décrit array_rotate_right () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 9282e31d4422172f71da33ba1eddc0322b0345fb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349650"
---
# <a name="array_rotate_right"></a>array_rotate_right()

`array_rotate_right()`fait pivoter les valeurs à l’intérieur d’un tableau vers la droite.

## <a name="syntax"></a>Syntaxe

`array_rotate_right(`*arr*, *rotate_count*`)`

## <a name="arguments"></a>Arguments

* *arr*: tableau d’entrée à fractionner, doit être un tableau dynamique.
* *rotate_count*: entier spécifiant le nombre de positions que les éléments de tableau feront pivoter vers la droite. Si la valeur est négative, les éléments sont pivotés vers la gauche.

## <a name="returns"></a>Retourne

Tableau dynamique contenant la même quantité d’éléments que dans le tableau d’origine, où chaque élément a été pivoté d’après *rotate_count*.

**Voir aussi**

* Pour faire pivoter un tableau sur la gauche, consultez [array_rotate_left ()](array_rotate_leftfunction.md).
* Pour décaler le tableau vers la gauche, consultez [array_shift_left ()](array_shift_leftfunction.md).
* Pour décaler le tableau vers la droite, consultez [array_shift_right ()](array_shift_rightfunction.md).

## <a name="examples"></a>Exemples

* Rotation vers la droite de deux positions :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, 2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[4, 5, 1, 2, 3]|

* Rotation à gauche de deux positions à l’aide d’une valeur rotate_count négative :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, -2)
    ```
    
    |arr|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5, 1, 2]|
