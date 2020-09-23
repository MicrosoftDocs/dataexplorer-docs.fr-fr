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
ms.openlocfilehash: 883aa2e22c02584de34c705b89a979b86e10475e
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102799"
---
# <a name="array_shift_left"></a>array_shift_left()

`array_shift_left()` déplace les valeurs à l’intérieur d’un tableau vers la gauche.

## <a name="syntax"></a>Syntaxe

`array_shift_left(`*`arr`*, *`shift_count`* `[,` *`fill_value`* ]`)`

## <a name="arguments"></a>Arguments

* *`arr`*: Tableau d’entrée à fractionner, doit être un tableau dynamique.
* *`shift_count`*: Entier spécifiant le nombre de positions que les éléments du tableau seront décalés vers la gauche. Si la valeur est négative, les éléments sont décalés vers la droite.
* *`fill_value`*: Valeur scalaire utilisée pour insérer des éléments à la place de ceux qui ont été déplacés et supprimés. Valeur par défaut : valeur null ou chaîne vide (selon le *`arr`* type).

## <a name="returns"></a>retourne :

Tableau dynamique contenant le même nombre d’éléments que dans le tableau d’origine. Chaque élément a été déplacé en fonction de *shift_count*. Les nouveaux éléments ajoutés à la place des éléments supprimés auront la valeur *fill_value*.

## <a name="see-also"></a>Voir aussi

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
