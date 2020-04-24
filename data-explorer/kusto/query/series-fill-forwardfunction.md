---
title: series_fill_forward() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_fill_forward () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6c79733aa1abf001f52eb07606c866904e370906
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508668"
---
# <a name="series_fill_forward"></a>series_fill_forward()

Effectue l’interpolation de remplissage vers l’avant des valeurs manquantes dans une série.

Prend une expression contenant un tableau numérique dynamique comme entrée, remplace tous les cas de missing_value_placeholder par la valeur la plus proche de son côté gauche autre que missing_value_placeholder et renvoie le tableau résultant. Les cas les plus à gauche de missing_value_placeholder sont préservés.

**Syntaxe**

`series_fill_forward(`*x*`[, `*missing_value_placeholder*`])`
* Retournera la série *x* avec toutes les instances de *missing_value_placeholder* remplis vers l’avant.

**Arguments**

* *x*: expression scalaire de tableau dynamique qui est un éventail de valeurs numériques. 
* *missing_value_placeholder*: paramètre facultatif qui spécifie un placeholder pour un remplacement des valeurs manquantes. La valeur `double`par défaut est *(nulle*).

**Remarques**

* Afin d’appliquer toutes les fonctions d’interpolation après [la série,](make-seriesoperator.md) il est recommandé de spécifier *nul* comme valeur par défaut: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* Le *missing_value_placeholder* peut être de n’importe quel type qui sera converti en types d’éléments réels. Par `double`conséquent, `long`soit ( `int`*nul*), (*nul*) ou (*nul*) ont le même sens.
* Si *missing_value_placeholder* est `double`*(nul)*(ou tout simplement omis qui ont le même sens), puis un résultat peut contenir des valeurs *nulles.* Utilisez d’autres fonctions d’interpolation pour les remplir. Actuellement, seuls [series_outliers()](series-outliersfunction.md) prennent en charge les valeurs *nulles* dans les tableaux d’entrée.
* Les fonctions conservent le type original d’éléments de tableau.

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

|Arr|fill_forward|
|---|---|
|[null,null,36,41,null,null,16,61,33,null,null]|[null,null,36,41,41,41,16,61,33,33,33]|
   
On peut utiliser [series_fill_backward](series-fill-backwardfunction.md) ou [série-remplissage-const](series-fill-constfunction.md) afin de compléter l’interpolation de la gamme ci-dessus.