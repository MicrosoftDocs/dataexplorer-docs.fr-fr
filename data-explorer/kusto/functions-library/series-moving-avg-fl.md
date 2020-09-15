---
title: series_moving_avg_fl ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur series_moving_avg_fl () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 24fcdb685d9238416e6652953ab47ff82f91bd72
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90075138"
---
# <a name="series_moving_avg_fl"></a>series_moving_avg_fl ()

Applique un filtre de moyenne mobile sur une série.

La fonction `series_moving_avg_fl()` prend une expression contenant un tableau numérique dynamique comme entrée et applique un filtre de [moyenne mobile simple](https://en.wikipedia.org/wiki/Moving_average#Simple_moving_average) .

> [!NOTE]
> Cette fonction est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md). Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`series_moving_avg_fl(`*y_series* `,` *n* `, [` *Centre*`])`
  
## <a name="arguments"></a>Arguments

* *y_series*: cellule de tableau dynamique de valeurs numériques.
* *n*: largeur du filtre de moyenne mobile.
* *Center*: valeur booléenne facultative qui indique si la moyenne mobile est l’une des options suivantes :
    * appliqué symétriquement sur une fenêtre avant et après le point actuel, ou 
    * appliqué sur une fenêtre à partir du point actuel vers l’arrière. <br>
    Par défaut, *Center* a la valeur false.

## <a name="usage"></a>Utilisation

`series_moving_avg_fl()` est une fonction définie par l’utilisateur. Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données. Il existe deux options d’utilisation : une utilisation ad hoc et une utilisation permanente. Consultez les onglets ci-dessous pour obtenir des exemples.

# <a name="ad-hoc"></a>[Ad hoc](#tab/adhoc)

Pour une utilisation ad hoc, incorporez son code à l’aide d’une [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_moving_avg_fl = (y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
;
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_fl(num, 5, True)
| render timechart 
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Pour une utilisation permanente, utilisez la [fonction. Create](../management/create-function.md). La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Installation unique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate moving average of specified width")
series_moving_avg_fl(y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
```

### <a name="usage"></a>Utilisation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_fl(num, 5, True)
| render timechart 
```

---

:::image type="content" source="images/series-moving-avg-fl/moving-average-five-bins.png" alt-text="Graphique illustrant la moyenne mobile de 5 emplacements" border="false":::
