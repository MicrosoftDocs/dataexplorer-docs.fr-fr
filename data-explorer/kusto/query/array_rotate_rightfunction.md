---
title: array_rotate_right() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit array_rotate_right() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 4f45db14f6ca2fe990f8d139c608ebed1915aa16
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518800"
---
# <a name="array_rotate_right"></a>array_rotate_right()

`array_rotate_right()`tourne les valeurs à l’intérieur d’un tableau vers la droite.

**Syntaxe**

`array_rotate_right(`*arr*, *rotate_count*`)`

**Arguments**

* *arr*: Le tableau d’entrée à diviser, doit être un tableau dynamique.
* *rotate_count*: Integer spécifiant le nombre de positions que les éléments de tableau seront tournés vers la droite. Si la valeur est négative, les éléments seront tournés vers la gauche.

**Retourne**

Tableau dynamique contenant la même quantité d’éléments que dans le tableau d’origine, où chaque élément a été tourné selon *rotate_count*.

**Voir aussi**

* Pour tourner le tableau vers la gauche, voir [array_rotate_left()](array_rotate_leftfunction.md).
* Pour déplacer le tableau vers la gauche, voir [array_shift_left()](array_shift_leftfunction.md).
* Pour déplacer le tableau vers la droite, voir [array_shift_right()](array_shift_rightfunction.md).

**Exemples**

* Rotation vers la droite par deux positions :

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, 2)
    ```
    
    |Arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[4,5,1,2,3]|

* Rotation vers la gauche par deux positions en utilisant la valeur négative rotate_count :

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, -2)
    ```
    
    |Arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,1,2]|