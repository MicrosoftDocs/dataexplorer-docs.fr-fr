---
title: series_fill_forward ()-Azure Explorateur de données
description: Cet article décrit series_fill_forward () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cdf9b84f684a2a4dfdb508f1ac5762039da8275d
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741704"
---
# <a name="series_fill_forward"></a>series_fill_forward()

Effectue une interpolation de remplissage vers l’avant des valeurs manquantes dans une série.

Une expression contenant un tableau numérique dynamique est l’entrée. La fonction remplace toutes les instances de missing_value_placeholder par la valeur la plus proche de son côté gauche de missing_value_placeholder, et retourne le tableau résultant. Les instances les plus à gauche de missing_value_placeholder sont conservées.

**Syntaxe**

`series_fill_forward(`*x*`[, `*missing_value_placeholder* x`])`
* Retourne la série *x* avec toutes les instances de *missing_value_placeholder* remplies.

**Arguments**

* *x*: expression scalaire de tableau dynamique, qui est un tableau de valeurs numériques. 
* *missing_value_placeholder*: paramètre facultatif, qui spécifie un espace réservé pour une valeur manquante à remplacer. La valeur par `double`défaut est (*null*).

**Remarques**

* Spécifiez *null* comme valeur par défaut pour appliquer les fonctions d’interpolation après [Make-Series](make-seriesoperator.md): 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* Le *missing_value_placeholder* peut être de n’importe quel type qui sera converti en types d’éléments réels. Les `double`deux*null*(null `long`) (*null*) `int`et (*null*) ont la même signification.
* Si missing_value_placeholder est (null) (ou omis-qui ont la même signification), un résultat peut contenir des valeurs *null* . Pour remplir ces valeurs *null* , utilisez d’autres fonctions d’interpolation. Actuellement [, seul series_outliers ()](series-outliersfunction.md) prend en charge les valeurs *null* dans les tableaux d’entrée.
* Les fonctions conservent le type d’origine des éléments de tableau.

**Exemple**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_forward(arr)  

```

|`arr`|`fill_forward`|
|---|---|
|[null, NULL, 36, 41, NULL, NULL, 16, 61, 33, NULL, NULL]|[null, NULL, 36, 41, 41, 41, 16, 61, 33, 33, 33]|
   
Utilisez [series_fill_backward](series-fill-backwardfunction.md) ou [le remplissage de série-const](series-fill-constfunction.md) pour terminer l’interpolation du tableau ci-dessus.