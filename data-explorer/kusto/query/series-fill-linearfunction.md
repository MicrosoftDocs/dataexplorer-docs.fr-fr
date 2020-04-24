---
title: series_fill_linear() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_fill_linear() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f9a12d172a1580d6a9e575b78b14404dce7820e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508651"
---
# <a name="series_fill_linear"></a>series_fill_linear()

Effectue l’interpolation linéaire des valeurs manquantes dans une série.

Prend une expression contenant un tableau numérique dynamique comme entrée, effectue l’interpolation linéaire pour tous les cas de missing_value_placeholder et renvoie le tableau résultant. Si le début et la fin du tableau contiennent missing_value_placeholder, il sera remplacé par la valeur la plus proche autre que missing_value_placeholder (peut être désactivé). Si l’ensemble du tableau se compose de la missing_value_placeholder alors le tableau sera rempli de constant_value ou 0 si aucun spécifié.  

**Syntaxe**

`series_fill_linear(`*x* `[,` *missing_value_placeholder*` [,`*fill_edges*` [,`*constant_value*`]]]))`
* Retournera l’interpolation linéaire de série de *x* en utilisant des paramètres spécifiés.
 

**Arguments**

* *x*: expression scalaire de tableau dynamique qui est un éventail de valeurs numériques.
* *missing_value_placeholder*: paramètre facultatif qui précise un espace réservé aux " valeurs manquantes " à remplacer. La valeur `double`par défaut est *(nulle*).
* *fill_edges*: Valeur Boolean qui indique si *missing_value_placeholder* au début et à la fin du tableau doivent être remplacés par la valeur la plus proche. *Vrai* par défaut. Si réglé à *faux,* puis *missing_value_placeholder* au début et à la fin du tableau sera préservé.
* *constant_value*: paramètre facultatif pertinent uniquement pour les tableaux entièrement se compose de valeurs *nulles,* ce qui spécifie la valeur constante pour remplir la série avec. La valeur par défaut est *de 0*. La définition de `double`ce paramètre (*nul*) laissera effectivement des valeurs *nulles* là où elles se trouvent.

**Remarques**

* Afin d’appliquer toutes les fonctions d’interpolation après [la série,](make-seriesoperator.md) il est recommandé de spécifier *nul* comme valeur par défaut: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* Le *missing_value_placeholder* peut être de n’importe quel type qui sera converti en types d’éléments réels. Par `double`conséquent, `long`soit ( `int`*nul*), (*nul*) ou (*nul*) ont le même sens.
* Si *missing_value_placeholder* est `double`*(nul)*(ou tout simplement omis qui ont le même sens), puis un résultat peut contenir des valeurs *nulles.* Utilisez d’autres fonctions d’interpolation pour les remplir. Actuellement, seuls [series_outliers()](series-outliersfunction.md) prennent en charge les valeurs *nulles* dans les tableaux d’entrée.
* La fonction conserve le type original d’éléments de tableau. Si *x* ne contient que des éléments *int* ou *longs,* alors l’interpolation linéaire retournera des valeurs interpolées arrondies plutôt que des valeurs exactes.

**Exemple**

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

|Arr|without_args|with_edges|wo_edges|with_const|
|---|---|---|---|---|
|[null,111.0,null,36.0,41.0,null,null,16.0.0.0.33.0,null,null]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[null,111.0,73.5,36.0.41.0.32.667.24.333.16.0.61.33.0,null,null]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|
|[null,111,null,36,41,null,null,16,61,33,null,null]|[111,111,73,36,41,32,24,16,61,33,33,33]|[111,111,73,36,41,32,24,16,61,33,33,33]|[null,111,73,36,41,32,24,16,61,33,null,null]|[111,111,74,38, 41,32,24,16,61,33,33,33]|
|[null, null,null,null]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[3.14159,3.14159,3.14159,3.14159]|