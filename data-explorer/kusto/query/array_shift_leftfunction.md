---
title: array_shift_left() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit array_shift_left() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 5b623bdcccc75261d0fb9324bb6015e16b0ff9b8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518783"
---
# <a name="array_shift_left"></a>array_shift_left()

`array_shift_left()`déplace les valeurs à l’intérieur d’un tableau vers la gauche.

**Syntaxe**

`array_shift_left(`*arr*, *shift_count* [, *fill_value* ]`)`

**Arguments**

* *arr*: Le tableau d’entrée à diviser, doit être un tableau dynamique.
* *shift_count*: Integer précise le nombre de positions que les éléments de tableau seront décalés vers la gauche. Si la valeur est négative, les éléments seront déplacés vers la droite.
* *fill_value*: Valeur Scalaire qui est utilisée pour l’insertion d’éléments au lieu de ceux qui ont été décalés et supprimés. Défaut : valeur nulle ou chaîne vide (selon le type *arr).*

**Retourne**

Tableau dynamique contenant la même quantité d’éléments que dans le tableau d’origine, où chaque élément a été déplacé en fonction *de shift_count*. Les nouveaux éléments qui sont ajoutés au lieu des éléments qui sont supprimés auront une valeur de *fill_value*.

**Voir aussi**

* Pour le tableau de déplacement à droite, voir [array_shift_right()](array_shift_rightfunction.md).
* Pour tourner à droite, voir [array_rotate_right()](array_rotate_rightfunction.md).
* Pour le tableau rotatif à gauche, voir [array_rotate_left()](array_rotate_leftfunction.md).

**Exemples**

* Déplacement vers la gauche par deux positions :

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,null,null]|

* Déplacement vers la gauche par deux positions et ajout de valeur par défaut :

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2, -1)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,-1,-1]|


* Passer à droite par deux positions en utilisant la valeur *négative shift_count* :

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, -2, -1)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[-1,-1,1,2,3]|
