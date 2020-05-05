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
ms.openlocfilehash: 22e40c810242ee82701cf2d0e382a9f1910ed22d
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741722"
---
# <a name="series_fill_const"></a>series_fill_const()

Remplace les valeurs manquantes dans une série par une valeur de constante spécifiée.

Accepte une expression contenant un tableau numérique dynamique comme entrée, remplace toutes les instances de missing_value_placeholder par les constant_value spécifiées et retourne le tableau résultant.

**Syntaxe**

`series_fill_const(`*x*`[, `*constant_value* constant_value`[,` *missing_value_placeholder*`]])`
* Renverra la série *x* avec toutes les instances de *missing_value_placeholder* remplacées par *constant_value*.

**Arguments**

* *x*: expression scalaire de tableau dynamique qui est un tableau de valeurs numériques.
* *constant_value*: paramètre qui spécifie un espace réservé pour une valeur manquante à remplacer. La valeur par défaut est *0*. 
* *missing_value_placeholder*: paramètre facultatif qui spécifie un espace réservé pour une valeur manquante à remplacer. La valeur par `double`défaut est (*null*).

**Remarques**
* Vous pouvez créer une série qui remplit une valeur constante à l’aide `default = ` de la syntaxe *DefaultValue* (ou simplement en omettant qui suppose 0). Pour plus d’informations, consultez [Make-Series](make-seriesoperator.md).

```kusto
make-series num=count() default=-1 on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* Pour appliquer des fonctions d’interpolation après [Make-Series](make-seriesoperator.md), spécifiez *null* comme valeur par défaut : 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* Le *missing_value_placeholder* peut être de n’importe quel type, qui sera converti en types d’éléments réels. Par conséquent, `double`(*null*), `long`(*null*) ou `int`(*null*) ont la même signification.
* La fonction conserve le type d’origine des éléments du tableau. 

**Exemple**

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