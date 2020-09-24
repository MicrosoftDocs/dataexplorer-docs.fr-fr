---
title: series_fill_const ()-Azure Explorateur de données
description: Cet article décrit series_fill_const () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8433773111f65e0271692bc3d1ba68cf0bc7c544
ms.sourcegitcommit: 44a4f7ea5c5d75301d7a09b7dc1254a1e5f08eaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210509"
---
# <a name="series_fill_const"></a>series_fill_const()

Remplace les valeurs manquantes dans une série par une valeur de constante spécifiée.

Accepte une expression contenant un tableau numérique dynamique comme entrée, remplace toutes les instances de missing_value_placeholder par le constant_value spécifié et retourne le tableau résultant.

## <a name="syntax"></a>Syntaxe

`series_fill_const(`*x* `, ` *constant_value* `[,` *missing_value_placeholder*`])`
* Renverra la série *x* avec toutes les instances de *missing_value_placeholder* remplacées par *constant_value*.

## <a name="arguments"></a>Arguments

* *x*: expression scalaire de tableau dynamique qui est un tableau de valeurs numériques.
* *constant_value*: valeur qui remplace les valeurs manquantes. 
* *missing_value_placeholder*: paramètre facultatif qui spécifie un espace réservé pour une valeur manquante à remplacer. La valeur par défaut est `double` (*null*).

**Notes**
* Si vous créez la série à l’aide de l’opérateur [Make-Series](make-seriesoperator.md) , elle remplit les valeurs manquantes par défaut 0, ou vous pouvez spécifier une valeur de constante à remplir en spécifiant `default = ` *DefaultValue* dans l’instruction make-Series.

```kusto
make-series num=count() default=-1 on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* Pour appliquer des fonctions d’interpolation après [Make-Series](make-seriesoperator.md), spécifiez *null* comme valeur par défaut : 

```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* Le *missing_value_placeholder* peut être de n’importe quel type, qui sera converti en types d’éléments réels. Par conséquent, `double` (*null*), `long` (*null*) ou `int` (*null*) ont la même signification.
* La fonction conserve le type d’origine des éléments du tableau. 

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(`arr`: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|`arr`|`fill_const1`|`fill_const2`|
|---|---|---|
|[111, NULL, 36, 41, 23, NULL, 16, 61, 33, NULL, NULL]|[111, 0.0, 36, 41, 23, 0.0, 16, 61, 33, 0.0, 0.0]|[111,-1, 36, 41, 23,-1, 16, 61, 33,-1,-1]|
