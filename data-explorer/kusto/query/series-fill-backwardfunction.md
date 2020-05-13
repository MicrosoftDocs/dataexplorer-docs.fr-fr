---
title: series_fill_backward ()-Azure Explorateur de données
description: Cet article décrit series_fill_backward () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bc26c61b9a94c6f21d2c53cae8fc80805b235f75
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372812"
---
# <a name="series_fill_backward"></a>series_fill_backward()

Effectue une interpolation de remplissage arrière des valeurs manquantes dans une série.

Une expression contenant un tableau numérique dynamique est l’entrée. La fonction remplace toutes les instances de missing_value_placeholder par la valeur la plus proche de son côté droit (autre que missing_value_placeholder) et retourne le tableau résultant. Les instances les plus à droite des missing_value_placeholder sont conservées.

**Syntaxe**

`series_fill_backward(`*x* `[, ` *missing_value_placeholder*`])`
* Retourne la série *x* avec toutes les instances de *missing_value_placeholder* remplies vers l’arrière.

**Arguments**

* *x*: expression scalaire de tableau dynamique, qui est un tableau de valeurs numériques.
* *missing_value_placeholder*: ce paramètre facultatif spécifie un espace réservé pour les valeurs manquantes. La valeur par défaut est `double` (*null*).

**Remarques**

* Spécifiez *null* comme valeur par défaut pour appliquer les fonctions d’interpolation après [Make-Series](make-seriesoperator.md): 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* Le *missing_value_placeholder* peut être de n’importe quel type qui sera converti en types d’éléments réels. Les deux `double` (*null*), `long` (*null*) et `int` (*null*) ont la même signification.
* Si *missing_value_placeholder* est `double` (*null*), (ou omis, qui ont la même signification), un résultat peut contenir des valeurs *null* . Pour remplir ces valeurs *null* , utilisez d’autres fonctions d’interpolation. Actuellement [, seul series_outliers ()](series-outliersfunction.md) prend en charge les valeurs *null* dans les tableaux d’entrée.
* La fonction conserve le type d’origine d’éléments de tableau.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_backward(arr)

```

|`arr`|`fill_forward`|
|---|---|
|[111, NULL, 36, 41, NULL, NULL, 16, 61, 33, NULL, NULL]|[111, 36, 36, 41, 16, 16, 16, 61, 33, NULL, NULL]|

  
Utilisez [series_fill_forward](series-fill-forwardfunction.md) ou [le remplissage de série-const](series-fill-constfunction.md) pour terminer l’interpolation du tableau ci-dessus.
