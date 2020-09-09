---
title: series_moving_avg_lf ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur series_moving_avg_lf () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 918881858a978fd10dc7089b408b0ad626f5c446
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614757"
---
# <a name="series_moving_avg_lf"></a>series_moving_avg_lf ()

Applique un filtre de moyenne mobile sur une série.

La fonction `series_moving_avg_lf()` prend une expression contenant un tableau numérique dynamique comme entrée et applique un filtre de [moyenne mobile simple](https://en.wikipedia.org/wiki/Moving_average#Simple_moving_average) .

> [!NOTE]
> Cette fonction est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md). Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`series_moving_avg_lf(`*y_series* `,` *n* `, [` *Centre*`])`
  
## <a name="arguments"></a>Arguments

* *y_series*: cellule de tableau dynamique de valeurs numériques.
* *n*: largeur du filtre de moyenne mobile.
* *Center*: valeur booléenne facultative qui indique si la moyenne mobile est l’une des options suivantes :
    * appliqué symétriquement sur une fenêtre avant et après le point actuel, ou 
    * appliqué sur une fenêtre à partir du point actuel vers l’arrière. <br>
    Par défaut, *Center* a la valeur false.

## <a name="usage"></a>Usage

`series_moving_avg_lf()` est une fonction définie par l’utilisateur. Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données. Il existe deux options d’utilisation : une utilisation ad hoc et une utilisation permanente. Consultez les onglets ci-dessous pour obtenir des exemples.

# <a name="ad-hoc"></a>[Ad hoc](#tab/adhoc)

Pour une utilisation ad hoc, incorporez son code à l’aide d’une [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.

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
| extend num_ma=series_moving_avg_lf(num, 5, True)
| render timechart 
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Pour une utilisation permanente, utilisez la [fonction. Create](../management/create-function.md). La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Installation unique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate moving average of specified width")
series_moving_avg_lf(y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
```

### <a name="usage"></a>Usage

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_lf(num, 5, True)
| render timechart 
```

---

:::image type="content" source="images/series-moving-avg-lf/moving-average-five-bins.png" alt-text="Graphique illustrant la moyenne mobile de 5 emplacements" border="false":::
