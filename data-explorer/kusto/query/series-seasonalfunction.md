---
title: series_seasonal ()-Azure Explorateur de données
description: Cet article décrit series_seasonal () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7997e6312f43316918c197c5ec10eec281495c63
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245932"
---
# <a name="series_seasonal"></a>series_seasonal()

Calcule le composant saisonnier d’une série, en fonction de la période saisonnière détectée ou donnée.

## <a name="syntax"></a>Syntaxe

`series_seasonal(`*série* `[,` *période*`])`

## <a name="arguments"></a>Arguments

* *série*: tableau dynamique numérique d’entrée
* *period* (facultatif) : nombre entier d’emplacements dans chaque période saisonnière, valeurs possibles :
    *  -1 (par défaut) : détecte automatiquement la période en utilisant [series_periods_detect ()](series-periods-detectfunction.md) avec un seuil de *0,7*. Retourne des zéros si le caractère saisonnier n’est pas détecté
    * Entier positif : utilisé comme période pour le composant saisonnier
    * Toute autre valeur : ignore le caractère saisonnier et retourne une série de zéros

## <a name="returns"></a>Retours

Tableau dynamique de même longueur que l’entrée de *série* qui contient le composant saisonnier calculé de la série. Le composant saisonnier est calculé comme la valeur *médiane* de toutes les valeurs qui correspondent à l’emplacement de l’emplacement, sur les périodes.

## <a name="examples"></a>Exemples

### <a name="auto-detect-the-period"></a>Détection automatique de la période

Dans l’exemple suivant, la période de la série est détectée automatiquement. La période de la première série est détectée comme étant six emplacements et les cinq autres emplacements. La troisième série « period » est trop petite pour être détectée et retourne une série de zéros. Consultez l’exemple suivant sur la [façon de forcer la période](#force-a-period).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2, 5, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1]|[1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0]|
|[8, 12, 14, 12, 10, 10, 12, 14, 12, 10, 10, 12, 14, 12, 10, 10, 12, 14, 12, 10]|[10.0, 12.0, 14.0, 12.0, 10.0, 10.0, 12.0, 14.0, 12.0, 10.0, 10.0, 12.0, 14.0, 12.0, 10.0, 10.0, 12.0, 14.0, 12.0, 10.0]|
|[1, 3, 5, 2, 4, 6, 1, 3, 5, 2, 4, 6]|[0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]|

### <a name="force-a-period"></a>Forcer une période

Dans cet exemple, la période de la série est trop petite pour être détectée par [series_periods_detect ()](series-periods-detectfunction.md), donc nous forçaons explicitement la période à atteindre le modèle saisonnier.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1, 3, 5, 1, 3, 5, 2, 4, 6]|[1.0, 3.0, 5.0, 1.0, 3.0, 5.0, 1.0, 3.0, 5.0]|
|[1, 3, 5, 2, 4, 6, 1, 3, 5, 2, 4, 6]|[1.5, 3.5, 5.5, 1.5, 3.5, 5.5, 1.5, 3.5, 5.5, 1.5, 3.5, 5.5]|
 
## <a name="next-steps"></a>Étapes suivantes

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)
