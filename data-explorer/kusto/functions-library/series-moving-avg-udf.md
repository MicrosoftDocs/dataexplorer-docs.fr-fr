---
title: series_moving_avg_udf ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur series_moving_avg_udf () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 526aa1c505dafa681665cccb0c8a8e1d56f80def
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557865"
---
# <a name="series_moving_avg_udf"></a>series_moving_avg_udf ()

Applique un filtre de moyenne mobile sur une série.

La fonction `series_moving_avg_udf()` prend une expression contenant un tableau numérique dynamique comme entrée et applique un filtre de [moyenne mobile simple](https://en.wikipedia.org/wiki/Moving_average#Simple_moving_average) .

> [!NOTE]
> Cette fonction est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md). Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`series_moving_avg_udf(`*y_series* `,` *n* `, [` *Centre*`])`
  
## <a name="arguments"></a>Arguments

* *y_series*: cellule de tableau dynamique de valeurs numériques.
* *n*: largeur du filtre de moyenne mobile.
* *Center*: valeur booléenne facultative qui indique si la moyenne mobile est l’une des options suivantes :
    * appliqué symétriquement sur une fenêtre avant et après le point actuel, ou 
    * appliqué sur une fenêtre à partir du point actuel vers l’arrière. <br>
    Par défaut, *Center* a la valeur false.

## <a name="usage"></a>Usage

* `series_moving_avg_udf()` est une fonction définie par l’utilisateur. Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données :
    * Pour une utilisation ad hoc, incorporez son code à l’aide d’une [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.
    * Pour une utilisation récurrente, conservez la fonction à l’aide de la [fonction. Create](../management/create-function.md). <br>
        La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

# <a name="ad-hoc-usage"></a>[Utilisation ad hoc](#tab/adhoc)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_moving_avg_udf = (y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
;
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_udf(num, 5, True)
| render timechart 
```

# <a name="persistent-usage"></a>[Utilisation permanente](#tab/persistent)

* **Installation unique**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate moving average of specified width")
series_moving_avg_udf(y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
```

* **Utilisation**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_udf(num, 5, True)
| render timechart 
```

---

:::image type="content" source="images/series-moving-avg-udf/moving-average-five-bins.png" alt-text="Graphique illustrant la moyenne mobile de 5 emplacements" border="false":::
