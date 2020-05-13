---
title: series_fill_linear ()-Azure Explorateur de données
description: Cet article décrit series_fill_linear () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4cec053990457a6b33c7446c5b32c63713320de9
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372761"
---
# <a name="series_fill_linear"></a>series_fill_linear()

Interpole de manière linéaire les valeurs manquantes dans une série.

Prend une expression contenant un tableau numérique dynamique comme entrée, effectue une interpolation linéaire pour toutes les instances de missing_value_placeholder et retourne le tableau résultant. Si le début et la fin du tableau contiennent missing_value_placeholder, il sera remplacé par la valeur la plus proche de missing_value_placeholder. Cette fonctionnalité peut être désactivée. Si le tableau entier se compose du missing_value_placeholder, le tableau est rempli avec constant_value ou 0 s’il n’est pas spécifié.  

**Syntaxe**

`series_fill_linear(`*x* `[,` *missing_value_placeholder* ` [,` *fill_edges* ` [,` *constant_value*`]]]))`
* Retourne une interpolation linéaire de série de *x* à l’aide des paramètres spécifiés.
 

**Arguments**

* *x*: expression scalaire de tableau dynamique, qui est un tableau de valeurs numériques.
* *missing_value_placeholder*: paramètre facultatif, qui spécifie un espace réservé pour les « valeurs manquantes » à remplacer. La valeur par défaut est `double` (*null*).
* *fill_edges*: valeur booléenne qui indique si *missing_value_placeholder* au début et à la fin du tableau doit être remplacé par la valeur la plus proche. *True* par défaut. Si la valeur est *false*, *missing_value_placeholder* au début et à la fin du tableau sera préservé.
* *constant_value*: le paramètre facultatif pertinent uniquement pour les tableaux est constitué entièrement de valeurs *null* . Ce paramètre spécifie une valeur de constante avec laquelle remplir la série. La valeur par défaut est *0*. L’affectation de la valeur `double` (*null*) à ce paramètre permet de conserver les valeurs *null* là où elles se trouvent.

**Remarques**

* Pour appliquer des fonctions d’interpolation après [Make-Series](make-seriesoperator.md), spécifiez *null* comme valeur par défaut : 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* Le *missing_value_placeholder* peut être de n’importe quel type qui sera converti en types d’éléments réels. Par conséquent, `double` (*null*), `long` (*null*) ou `int` (*null*) ont la même signification.
* Si *missing_value_placeholder* est `double` (*null*) (ou omis, qui ont la même signification), un résultat peut contenir des valeurs *null* . Utilisez d’autres fonctions d’interpolation pour remplir ces valeurs *null* . Actuellement [, seul series_outliers ()](series-outliersfunction.md) prend en charge les valeurs *null* dans les tableaux d’entrée.
* La fonction conserve le type d’origine d’éléments de tableau. Si x contient uniquement des éléments int ou long, l’interpolation linéaire retourne des valeurs interpolées arrondies plutôt que des valeurs exactes.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null, 111.0, null, 36.0, 41.0, null, null, 16.0, 61.0, 33.0, null, null]), // Array of double    
    dynamic([null, 111,   null, 36,   41,   null, null, 16,   61,   33,   null, null]), // Similar array of int
    dynamic([null, null, null, null])                                                   // Array with missing values only
];
data
| project arr, 
          without_args = series_fill_linear(arr),
          with_edges = series_fill_linear(arr, double(null), true),
          wo_edges = series_fill_linear(arr, double(null), false),
          with_const = series_fill_linear(arr, double(null), true, 3.14159)  

```

|`arr`|`without_args`|`with_edges`|`wo_edges`|`with_const`|
|---|---|---|---|---|
|[null, 111.0, NULL, 36.0, 41.0, NULL, NULL, 16, 61,0, 33.0, NULL, NULL]|[111.0, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, 16, 61,0, 33.0, 33.0, 33.0]|[111.0, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, 16, 61,0, 33.0, 33.0, 33.0]|[null, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, 16, 61,0, 33.0, NULL, NULL]|[111.0, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, 16, 61,0, 33.0, 33.0, 33.0]|
|[null, 111, NULL, 36, 41, NULL, NULL, 16, 61, 33, NULL, NULL]|[111111, 73, 36, 41, 32, 24, 16, 61, 33, 33, 33]|[111111, 73, 36, 41, 32, 24, 16, 61, 33, 33, 33]|[null, 111, 73, 36, 41, 32, 24, 16, 61, 33, NULL, NULL]|[111111, 74, 38, 41, 32, 24, 16, 61, 33, 33, 33]|
|[null, NULL, NULL, NULL]|[0.0, 0.0, 0.0, 0.0]|[0.0, 0.0, 0.0, 0.0]|[0.0, 0.0, 0.0, 0.0]|[3,14159, 3,14159, 3,14159, 3,14159]|
