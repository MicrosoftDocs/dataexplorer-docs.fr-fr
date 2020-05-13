---
title: series_seasonal ()-Azure Explorateur de données
description: Cet article décrit series_seasonal () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 88160a55ba8342e3ed6bce90ec77c5a370ab7358
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372466"
---
# <a name="series_seasonal"></a>series_seasonal()

Calcule le composant saisonnier d’une série en fonction de la période saisonnière détectée ou donnée.

**Syntaxe**

`series_seasonal(`*série* `[,` *période*`])`

**Arguments**

* *série*: tableau dynamique numérique d’entrée
* *period* (facultatif) : nombre entier d’emplacements dans chaque période saisonnière, valeurs possibles :
    *  -1 (par défaut) : détecter automatiquement la période à l’aide de [series_periods_detect ()](series-periods-detectfunction.md) avec un seuil de *0,7*, retourne des zéros si le caractère saisonnier n’est pas détecté
    * entier positif : sera utilisé comme période pour le composant saisonnier
    * toute autre valeur : ignorer le caractère saisonnier et retourner une série de zéros

**Retourne**

Tableau dynamique de même longueur que l’entrée de *série* contenant le composant saisonnier calculé de la série. Le composant saisonnier est calculé en tant que *médiane* de toutes les valeurs correspondant à l’emplacement de l’emplacement sur les différentes périodes.

**Voir aussi :**

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)

**Exemples**

**1. détection automatique de la période**

Dans l’exemple suivant, la période de la série est détectée automatiquement, la période de la première série est détectée comme étant égale à 6 emplacements et les 5 autres emplacements. la troisième série est trop petite pour être détectée et renvoie une série de zéros (Voir l’exemple suivant sur la manière de forcer la période).

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



**2. forcer une période**

Dans l’exemple suivant, la période de la série est trop petite pour être détectée par [series_periods_detect ()](series-periods-detectfunction.md) , c’est pourquoi nous forçant la période explicitement à se procurer le modèle saisonnier.

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
