---
title: array_rotate_left() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit array_rotate_left() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 82eb8c24a3ed04146e5416b020bda7085426d138
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518817"
---
# <a name="array_rotate_left"></a>array_rotate_left()

`array_rotate_left()`tourne les valeurs à l’intérieur d’un tableau vers la gauche.

**Syntaxe**

`array_rotate_left(`*arr*, *rotate_count*`)`

**Arguments**

* *arr*: Le tableau d’entrée à diviser, doit être un tableau dynamique.
* *rotate_count*: Integer précisant le nombre de positions que les éléments de tableau seront tournés vers la gauche. Si la valeur est négative, les éléments seront tournés vers la droite.

**Retourne**

Tableau dynamique contenant la même quantité d’éléments que dans le tableau d’origine, où chaque élément a été tourné selon *rotate_count*.

**Voir aussi**

* Pour tourner le tableau vers la droite, voir [array_rotate_right()](array_rotate_rightfunction.md).
* Pour déplacer le tableau vers la gauche, voir [array_shift_left()](array_shift_leftfunction.md).
* Pour déplacer le tableau vers la droite, voir [array_shift_right()](array_shift_rightfunction.md).

**Exemples**

* Rotation vers la gauche par deux positions :

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, 2)
    ```
    
    |Arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,1,2]|

* Rotation vers la droite par deux positions en utilisant la valeur négative rotate_count :

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, -2)
    ```
    
    |Arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[4,5,1,2,3]|