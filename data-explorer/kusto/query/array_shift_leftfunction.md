---
title: array_shift_left ()-Azure Explorateur de données
description: Cet article décrit array_shift_left () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 76fcb66e25ba9279e1b98fe60ba3a5e59a299845
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349633"
---
# <a name="array_shift_left"></a>array_shift_left()

`array_shift_left()`déplace les valeurs à l’intérieur d’un tableau vers la gauche.

## <a name="syntax"></a>Syntaxe

`array_shift_left(`*`arr`*, *`shift_count`* `[,` *`fill_value`* ]`)`

## <a name="arguments"></a>Arguments

* *`arr`*: Tableau d’entrée à fractionner, doit être un tableau dynamique.
* *`shift_count`*: Entier spécifiant le nombre de positions que les éléments du tableau seront décalés vers la gauche. Si la valeur est négative, les éléments sont décalés vers la droite.
* *`fill_value`*: Valeur scalaire utilisée pour insérer des éléments à la place de ceux qui ont été déplacés et supprimés. Valeur par défaut : valeur null ou chaîne vide (selon le *`arr`* type).

## <a name="returns"></a>Retourne

Tableau dynamique contenant le même nombre d’éléments que dans le tableau d’origine. Chaque élément a été déplacé en fonction de *shift_count*. Les nouveaux éléments ajoutés à la place des éléments supprimés auront la valeur *fill_value*.

**Voir aussi**

* Pour déplacer le tableau à droite, consultez [array_shift_right ()](array_shift_rightfunction.md).
* Pour faire pivoter un tableau vers la droite, consultez [array_rotate_right ()](array_rotate_rightfunction.md).
* Pour faire pivoter le tableau vers la gauche, consultez [array_rotate_left ()](array_rotate_leftfunction.md).

## <a name="examples"></a>Exemples

* Décalage vers la gauche de deux positions :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5, NULL, NULL]|

* Décalage vers la gauche de deux positions et ajout de la valeur par défaut :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2, -1)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5,-1,-1]|


* Décalage vers la droite de deux positions à l’aide d’une valeur *shift_count* négative :

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, -2, -1)
    ```
    
    |`arr`|`arr_shift`|
    |---|---|
    |[1, 2, 3, 4, 5]|[-1,-1, 1, 2, 3]|
