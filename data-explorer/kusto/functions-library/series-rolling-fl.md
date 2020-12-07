---
title: series_rolling_fl ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur series_rolling_fl () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: fe9ea2556346edaf0327e0d9ce50d4d4519d4bb3
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321792"
---
# <a name="series_rolling_fl"></a>series_rolling_fl()


La fonction `series_rolling_fl()` applique l’agrégation dynamique sur une série. Elle prend une table contenant plusieurs séries (tableau numérique dynamique) et s’applique, pour chaque série, une fonction d’agrégation propagée.

> [!NOTE]
> * `series_rolling_fl()` est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md).
> * Cette fonction contient python inline et nécessite l' [activation du plug-in Python ()](../query/pythonplugin.md#enable-the-plugin) sur le cluster. Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`T | invoke series_rolling_fl(`*y_series* `,` *y_rolling_series* `,` *n & e* `,` *aggr* `,` *aggr_params* `,` *Center*`)`

## <a name="arguments"></a>Arguments

* *y_series*: nom de la colonne (de la table d’entrée) contenant la série à ajuster.
* *y_rolling_series*: nom de la colonne dans laquelle stocker la série d’agrégations propagées.
* *n*: largeur de la fenêtre dynamique.
* *aggr*: nom de la fonction d’agrégation à utiliser. Consultez [fonctions d’agrégation](#aggregation-functions).
* *aggr_params*: paramètres facultatifs pour la fonction d’agrégation.
* *Center*: valeur booléenne facultative qui indique si la fenêtre propagée est l’une des options suivantes :
    * appliqué symétriquement avant et après le point actuel, ou 
    * appliqué à partir du point actuel vers l’arrière. <br>
    Par défaut, *Center* a la valeur false pour le calcul des données de streaming.

## <a name="aggregation-functions"></a>Fonctions d’agrégation

Cette fonction prend en charge n’importe quelle fonction d’agrégation de [numpy](https://numpy.org/) ou [scipy. stats](https://docs.scipy.org/doc/scipy/reference/stats.html#module-scipy.stats) , qui calcule une scalaire à partir d’une série. La liste suivante n’est pas exhaustive :

* [`sum`](https://numpy.org/doc/stable/reference/generated/numpy.sum.html#numpy.sum) 
* [`mean`](https://numpy.org/doc/stable/reference/generated/numpy.mean.html?highlight=mean#numpy.mean)
* [`min`](https://numpy.org/doc/stable/reference/generated/numpy.amin.html#numpy.amin)
* [`max`](https://numpy.org/doc/stable/reference/generated/numpy.amax.html)
* [`ptp (max-min)`](https://numpy.org/doc/stable/reference/generated/numpy.ptp.html)
* [`percentile`](https://numpy.org/doc/stable/reference/generated/numpy.percentile.html)
* [`median`](https://numpy.org/doc/stable/reference/generated/numpy.median.html)
* [`std`](https://numpy.org/doc/stable/reference/generated/numpy.std.html)
* [`var`](https://numpy.org/doc/stable/reference/generated/numpy.var.html)
* [`gmean` (moyenne géométrique)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.gmean.html)
* [`hmean` (moyenne harmonique)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.hmean.html)
* [`mode` (valeur la plus courante)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mode.html)
* [`moment` (n<sup>ième</sup> moment)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.moment.html)
* [`tmean` (moyenne rognée)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmean.html)
* [`tmin`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmin.html) 
* [`tmax`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmax.html)
* [`tstd`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tstd.html)
* [`iqr` (intervalle entre quantile)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.iqr.html) 

## <a name="usage"></a>Utilisation

`series_rolling_fl()` est une [fonction tabulaire](../query/functions/user-defined-functions.md#tabular-function)définie par l’utilisateur, à appliquer à l’aide de l' [opérateur Invoke](../query/invokeoperator.md). Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données. Il existe deux options d’utilisation : une utilisation ad hoc et une utilisation permanente. Consultez les onglets ci-dessous pour obtenir des exemples.

# <a name="ad-hoc"></a>[Ad hoc](#tab/adhoc)

Pour une utilisation ad hoc, incorporez son code à l’aide de l' [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rolling_fl = (tbl:(*), y_series:string, y_rolling_series:string, n:int, aggr:string, aggr_params:dynamic=dynamic([null]), center:bool=true)
{
    let kwargs = pack('y_series', y_series, 'y_rolling_series', y_rolling_series, 'n', n, 'aggr', aggr, 'aggr_params', aggr_params, 'center', center);
    let code =
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_rolling_series = kargs["y_rolling_series"]\n'
        'n = kargs["n"]\n'
        'aggr = kargs["aggr"]\n'
        'aggr_params = kargs["aggr_params"]\n'
        'center = kargs["center"]\n'
        'result = df\n'
        'in_s = df[y_series]\n'
        'func = getattr(np, aggr, None)\n'
        'if not func:\n'
        '    import scipy.stats\n'
        '    func = getattr(scipy.stats, aggr)\n'
        'if func:\n'
        '    result[y_rolling_series] = list(pd.Series(in_s[i]).rolling(n, center=center, min_periods=1).apply(func, args=aggr_params).values for i in range(len(in_s)))\n'
        '\n';
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
;
//
//  Calculate rolling median of 9 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_med = dynamic(null)
| invoke series_rolling_fl('num', 'rolling_med', 9, 'median')
| render timechart
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Pour une utilisation permanente, utilisez [`.create function`](../management/create-function.md) . La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Installation unique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Rolling window functions on a series")
series_rolling_fl(tbl:(*), y_series:string, y_rolling_series:string, n:int, aggr:string, aggr_params:dynamic, center:bool=true)
{
    let kwargs = pack('y_series', y_series, 'y_rolling_series', y_rolling_series, 'n', n, 'aggr', aggr, 'aggr_params', aggr_params, 'center', center);
    let code =
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_rolling_series = kargs["y_rolling_series"]\n'
        'n = kargs["n"]\n'
        'aggr = kargs["aggr"]\n'
        'aggr_params = kargs["aggr_params"]\n'
        'center = kargs["center"]\n'
        'result = df\n'
        'in_s = df[y_series]\n'
        'func = getattr(np, aggr, None)\n'
        'if not func:\n'
        '    import scipy.stats\n'
        '    func = getattr(scipy.stats, aggr)\n'
        'if func:\n'
        '    result[y_rolling_series] = list(pd.Series(in_s[i]).rolling(n, center=center, min_periods=1).apply(func, args=aggr_params).values for i in range(len(in_s)))\n'
        '\n';
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Utilisation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Calculate rolling median of 9 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_med = dynamic(null)
| invoke series_rolling_fl('num', 'rolling_med', 9, 'median', dynamic([null]))
| render timechart
```

---

:::image type="content" source="images/series-rolling-fl/rolling-median-9.png" alt-text="Graphique illustrant la médiane de roulement de 9 éléments" border="false":::

## <a name="additional-examples"></a>Exemples supplémentaires

Les exemples suivants supposent que la fonction est déjà installée :

1. Calculer le nombre minimal de minutes & le 75e centile de 15 éléments
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    //
    //  Calculate rolling min, max & 75th percentile of 15 elements
    //
    demo_make_series1
    | make-series num=count() on TimeStamp step 1h by OsVer
    | extend rolling_min = dynamic(null), rolling_max = dynamic(null), rolling_pct = dynamic(null)
    | invoke series_rolling_fl('num', 'rolling_min', 15, 'min', dynamic([null]))
    | invoke series_rolling_fl('num', 'rolling_max', 15, 'max', dynamic([null]))
    | invoke series_rolling_fl('num', 'rolling_pct', 15, 'percentile', dynamic([75]))
    | render timechart
    ```
    
    :::image type="content" source="images/series-rolling-fl/graph-rolling-15.png" alt-text="Graphique illustrant le nombre minimal, maximal & 75e centile de 15 éléments" border="false":::

1. Calculer la moyenne tronquée
        
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    //
    //  Calculate rolling trimmed mean
    //
    range x from 1 to 100 step 1
    | extend y=iff(x % 13 == 0, 2.0, iff(x % 23 == 0, -2.0, rand()))
    | summarize x=make_list(x), y=make_list(y)
    | extend yr = dynamic(null)
    | invoke series_rolling_fl('y', 'yr', 7, 'tmean', pack_array(pack_array(-2, 2), pack_array(false, false))) //  trimmed mean: ignoring values outside [-2,2] inclusive
    | render linechart
    ```
    
    :::image type="content" source="images/series-rolling-fl/rolling-trimmed-mean.png" alt-text="Graphique illustrant la moyenne tronquée" border="false":::
    
