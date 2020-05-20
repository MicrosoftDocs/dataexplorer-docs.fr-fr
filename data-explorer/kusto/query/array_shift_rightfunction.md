---
title: array_shift_right ()-Azure Explorateur de données
description: Cet article décrit array_shift_right () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 28a44365d6d79bf30ec188146d989f2af2ad12c1
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550654"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()`déplace les valeurs à l’intérieur d’un tableau vers la droite.

**Syntaxe**

`array_shift_right(`*`arr`*, *`shift_count`* [, *`fill_value`* ]`)`

**Arguments**

* *`arr`*: Tableau d’entrée à fractionner, doit être un tableau dynamique.
* *`shift_count`*: Entier spécifiant le nombre de positions que les éléments du tableau seront décalés vers la droite. Si la valeur est négative, les éléments sont décalés vers la gauche.
* *`fill_value`*: valeur scalaire utilisée pour insérer des éléments à la place de ceux qui ont été déplacés et supprimés. Valeur par défaut : valeur null ou chaîne vide (selon le type *arr* ).

**Retourne**

Tableau dynamique contenant la même quantité d’éléments que dans le tableau d’origine. Chaque élément a été déplacé en fonction de *`shift_count`* . Les nouveaux éléments ajoutés à la place des éléments supprimés auront la valeur *`fill_value`* .

**Voir aussi**

* Pour décaler le tableau à gauche, consultez [array_shift_left ()](array_shift_leftfunction.md).
* Pour faire pivoter un tableau vers la droite, consultez [array_rotate_right ()](array_rotate_rightfunction.md).
* Pour faire pivoter le tableau vers la gauche, consultez [array_rotate_left ()](array_rotate_leftfunction.md).

**Exemples**

* Décalage vers la droite de deux positions :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[null, NULL, 1, 2, 3]|

* Le décalage vers la droite de deux positions et l’ajout d’une valeur par défaut :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[-1,-1, 1, 2, 3]|

* Décalage vers la gauche de deux positions à l’aide d’une valeur de shift_count négative :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |arr|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5,-1,-1]|
    