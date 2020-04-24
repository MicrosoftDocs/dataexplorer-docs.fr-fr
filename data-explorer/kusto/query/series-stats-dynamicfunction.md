---
title: series_stats_dynamic() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_stats_dynamic() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: b667af6d037b0b316bf18406e1fb49528e390213
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507903"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

Retourne les statistiques d’une série dans un objet dynamique.  

La `series_stats_dynamic()` fonction prend une colonne contenant un tableau numérique dynamique comme entrée et génère une valeur dynamique avec le contenu suivant :
* `min`: valeur minimale dans le tableau d’entrée
* `min_idx`: première position de la valeur minimale dans le tableau d’entrée
* `max`: valeur maximale dans le tableau d’entrée
* `max_idx`: première position de la valeur maximale dans le tableau d’entrée
* `avg`: valeur moyenne du tableau d’entrée
* `variance`: écart d’échantillon de tableau d’entrée
* `stdev`: échantillon d’écart standard du tableau d’entrée

**Syntaxe**

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite*`])`

**Arguments**

* *x*: Cellule de tableau dynamique qui est un éventail de valeurs numériques. 
* *ignore_nonfinite*: Boolean (facultatif, par défaut `false`: ) drapeau qui précise s’il faut calculer les statistiques tout en ignorant les valeurs non finies *(nulles,* *NaN*, *inf,* etc.). Si le `false` résultat retourné `null` est si les valeurs non-finies sont présentes dans le tableau.

**Exemple**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|"min": 2.0, "min_idx": 8, "max": 87.0, "max_idx": 3, "avg": 32.8, "stdev": 28.50363853548269, "variance": 812.457142222222222222222222222222219