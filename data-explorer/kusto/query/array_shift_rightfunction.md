---
title: array_shift_right() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit array_shift_right () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 73f87d4c5ce1a841404e438e0e5647089b38785f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518766"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()`déplace les valeurs à l’intérieur d’un tableau vers la droite.

**Syntaxe**

`array_shift_right(`*arr*, *shift_count* [, *fill_value* ]`)`

**Arguments**

* *arr*: Le tableau d’entrée à diviser, doit être un tableau dynamique.
* *shift_count*: Integer précise le nombre de positions que les éléments de tableau seront décalés vers la droite. Si la valeur est négative, les éléments seront déplacés vers la gauche.
* *fill_value*: valeur scalaire utilisée pour l’insertion d’éléments au lieu de ceux qui ont été décalés et supprimés. Défaut : valeur nulle ou chaîne vide (selon le type *arr).*

**Retourne**

Tableau dynamique contenant la même quantité d’éléments que dans le tableau d’origine, où chaque élément a été déplacé en fonction *de shift_count*. Les nouveaux éléments qui sont ajoutés au lieu des éléments qui sont supprimés auront une valeur de *fill_value*.

**Voir aussi**

* Pour le tableau de déplacement à gauche, voir [array_shift_left()](array_shift_leftfunction.md).
* Pour tourner à droite, voir [array_rotate_right()](array_rotate_rightfunction.md).
* Pour le tableau rotatif à gauche, voir [array_rotate_left()](array_rotate_leftfunction.md).

**Exemples**

* Déplacement vers la droite par deux positions :

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[null,null,1,2,3]|

* Déplacement vers la droite par deux positions et ajout d’une valeur par défaut :

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[-1,-1,1,2,3]|


* Déplacement vers la gauche par deux positions en utilisant une valeur négative shift_count :

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,-1,-1]|