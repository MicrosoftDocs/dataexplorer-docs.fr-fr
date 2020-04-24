---
title: series_fill_const() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_fill_const() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7f5f939068737bdd6519fc0c63b663d19ae076a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508770"
---
# <a name="series_fill_const"></a>series_fill_const()

Remplace les valeurs manquantes d’une série par une valeur constante spécifiée.

Prend une expression contenant un tableau numérique dynamique comme entrée, remplace tous les cas de missing_value_placeholder par des constant_value spécifiés et renvoie le tableau résultant.

**Syntaxe**

`series_fill_const(`*x*`[, `*missing_value_placeholder de* `[,` *constant_value*`]])`
* Retournera la série *x* avec tous les cas de *missing_value_placeholder* remplacé par *constant_value*.

**Arguments**

* *x*: expression scalaire de tableau dynamique qui est un éventail de valeurs numériques.
* *constant_value*: paramètre qui précise un espace réservé pour le remplacement d’une valeur manquante. La valeur par défaut est *de 0*. 
* *missing_value_placeholder*: paramètre facultatif qui spécifie un placeholder pour un remplacement des valeurs manquantes. La valeur `double`par défaut est *(nulle*).

**Remarques**
* Il est possible de créer une série `default = ` avec remplissage constant dans un appel en utilisant la syntaxe *DefaultValue* (ou tout simplement l’omission qui assumera 0). Voir [make-series](make-seriesoperator.md) pour plus d’informations.

```kusto
make-series num=count() default=-1 on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* Afin d’appliquer toutes les fonctions d’interpolation après [la série,](make-seriesoperator.md) il est recommandé de spécifier *nul* comme valeur par défaut: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* Le *missing_value_placeholder* peut être de n’importe quel type qui sera converti en types d’éléments réels. Par `double`conséquent, `long`soit ( `int`*nul*), (*nul*) ou (*nul*) ont le même sens.
* La fonction conserve le type original des éléments de tableau. 

**Exemple**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|Arr|fill_const1|fill_const2|
|---|---|---|
|[111,null,36,41,23,null,16,61,33,null,null]|[111,0.0,36,41,23,0.0,16,61,33,0.0,0.0]|[111,-1,36,41,23,-1,16,61,33,-1,-1]|