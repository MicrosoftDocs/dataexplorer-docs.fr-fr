---
title: series_stats() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_stats () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 07aa5df7351a5d4be1522d39456423197bde508d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507920"
---
# <a name="series_stats"></a>series_stats()

`series_stats()`retourne des statistiques pour une série dans plusieurs colonnes.  

La `series_stats()` fonction prend une colonne contenant un tableau numérique dynamique comme entrée et calcule les colonnes suivantes :
* `min`: valeur minimale dans le tableau d’entrée
* `min_idx`: première position de la valeur minimale dans le tableau d’entrée
* `max`: valeur maximale dans le tableau d’entrée
* `max_idx`: première position de la valeur maximale dans le tableau d’entrée
* `avg`: valeur moyenne du tableau d’entrée
* `variance`: écart d’échantillon de tableau d’entrée
* `stdev`: échantillon d’écart standard du tableau d’entrée

> [!NOTE] 
> Cette fonction renvoie plusieurs colonnes de sorte qu’il ne peut pas être utilisé comme argument pour une autre fonction.

**Syntaxe**

projet `series_stats(` *x* `[,` *ignore_nonfinite* `])` ou `series_stats(`étendre *x* `)` Returns toutes les colonnes mentionnées ci-dessus avec les noms suivants : series_stats_x_min, series_stats_x_min_idx et etc.
 
projet (m,`series_stats(`mi)*x* `)` ou étendre (m, mi)`series_stats(`*x* `)` Retourne les colonnes suivantes : m (min) et mi (min_idx).

**Arguments**

* *x*: Cellule de tableau dynamique, qui est un éventail de valeurs numériques. 
* *ignore_nonfinite*: Boolean (facultatif, par défaut `false`: ) drapeau qui précise s’il faut calculer les statistiques tout en ignorant les valeurs non finies *(nulles,* *NaN*, *inf,* etc.). S’il `false`est fixé à `null` , les valeurs retournées seraient si les valeurs non-finies sont présentes dans le tableau.

**Exemple**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32,8|28.5036338535483|812.457142857143|
