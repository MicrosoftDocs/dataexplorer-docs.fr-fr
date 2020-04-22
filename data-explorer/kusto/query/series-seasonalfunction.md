---
title: series_seasonal() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_seasonal () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 559731ab7dca2e49e124a856ca7a66a912583b62
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663131"
---
# <a name="series_seasonal"></a>series_seasonal()

Calcule la composante saisonnière d’une série en fonction de la période saisonnière détectée ou donnée.

**Syntaxe**

`series_seasonal(`*période de* *série* `[,``])`

**Arguments**

* *série*: Tableau dynamique numérique d’entrée
* *période* (facultatif) : Nombre de bacs dans chaque période saisonnière, valeurs possibles :
    *  -1 (par défaut): l’automobile détecte la période à [l’aide de series_periods_detect()](series-periods-detectfunction.md) avec un seuil de *0,7*, retourne zéros si la saisonnalité n’est pas détectée
    * l’intégrant positif : sera utilisé comme période pour la composante saisonnière
    * toute autre valeur: ignorer la saisonnalité et retourner une série de zéros

**Retourne**

Gamme dynamique de la même longueur que l’entrée *de la série* contenant la composante saisonnière calculée de la série. La composante saisonnière est calculée comme la *médiane* de toutes les valeurs correspondant à l’emplacement du bac sur toutes les périodes.

**Voir aussi :**

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)

**Exemples**

**1. Auto détecter la période**

Dans l’exemple suivant, la période de la série est automatiquement détectée, la période de la première série est détectée à 6 bacs et les 5 deuxième bacs, la période de la troisième série est trop courte pour être détectée et renvoie une série de zéros (voir l’exemple suivant sur la façon de forcer la période).

```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1]|[1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0]|
|[8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]|[10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0]|
|[1,3,5,2,4,6,1,3,5,2,4,6]|[0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0]|



**2. Forcer une période**

Dans l’exemple suivant, la période de la série est trop courte pour être détectée par [series_periods_detect()](series-periods-detectfunction.md) de sorte que nous forçons la période explicitement pour obtenir le modèle saisonnier.

```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1,3,5,1,3,5,2,4,6]|[1.0,3.0,5.0,1.0,3.0,5.0,1.0,3.0,5.0]|
|[1,3,5,2,4,6,1,3,5,2,4,6]|[1.5,3.5,5.5,1.5,3.5,5.5,1.5,3.5,5.5,1.5,3.5,5.5]|
