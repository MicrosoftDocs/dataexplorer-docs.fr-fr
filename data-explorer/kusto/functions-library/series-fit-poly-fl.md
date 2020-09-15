---
title: series_fit_poly_fl ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur series_fit_poly_fl () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 3834e3189a62cd8581d2e3607acad9c7d8c8d7c2
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90075153"
---
# <a name="series_fit_poly_fl"></a>series_fit_poly_fl ()

La fonction `series_fit_poly_fl()` applique une régression polynomiale sur une série. Elle prend une table qui contient plusieurs séries (tableau numérique dynamique) et génère, pour chaque série, le polynôme à poids fort qui le contiendra le mieux à l’aide de la [régression polynomiale](https://en.wikipedia.org/wiki/Polynomial_regression). Cette fonction retourne à la fois les coefficients polynomiaux et le polynôme interpolé sur la plage de la série.

> [!NOTE]
> `series_fit_poly_fl()` est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md). Cette fonction contient python inline et nécessite l' [activation du plug-in Python ()](../query/pythonplugin.md#enable-the-plugin) sur le cluster. Pour plus d’informations, consultez [utilisation](#usage). Pour la régression linéaire d’une série espacée uniformément, telle qu’elle est créée par l' [opérateur make-Series](../query/make-seriesoperator.md), utilisez la fonction native [series_fit_line ()](../query/series-fit-linefunction.md).

## <a name="syntax"></a>Syntaxe

`T | invoke series_fit_poly_fl(`*y_series* `,` *y_fit_series* `,` *fit_coeff* `,` *degré* `, [` *x_series* `,` *x_istime*]`)`
  
## <a name="arguments"></a>Arguments

* *y_series*: nom de la colonne de la table d’entrée contenant la [variable dépendante](https://en.wikipedia.org/wiki/Dependent_and_independent_variables). Autrement dit, la série à ajuster.
* *y_fit_series*: nom de la colonne dans laquelle stocker la série la mieux adaptée.
* *fit_coeff*: nom de la colonne dans laquelle stocker les coefficients polynomiaux les mieux adaptés.
* *degree*: ordre requis du polynôme à ajuster. Par exemple, 1 pour la régression linéaire, 2 pour la régression quadratique, et ainsi de suite.
* *x_series*: nom de la colonne contenant la [variable indépendante](https://en.wikipedia.org/wiki/Dependent_and_independent_variables), autrement dit, l’axe x ou l’axe de temps. Ce paramètre est facultatif et n’est nécessaire que pour les [séries espacées](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series)de manière inégale. La valeur par défaut est une chaîne vide, car x est redondant pour la régression d’une série espacée de manière égale.
* *x_istime*: ce paramètre booléen est facultatif. Ce paramètre est nécessaire uniquement si *x_series* est spécifié et qu’il s’agit d’un vecteur de DateTime.

## <a name="usage"></a>Utilisation

`series_fit_poly_fl()`[fonction tabulaire](../query/functions/user-defined-functions.md#tabular-function)définie par l’utilisateur, à appliquer à l’aide de l' [opérateur Invoke](../query/invokeoperator.md). Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données. Il existe deux options d’utilisation : une utilisation ad hoc et une utilisation permanente. Consultez les onglets ci-dessous pour obtenir des exemples.

# <a name="ad-hoc"></a>[Ad hoc](#tab/adhoc)

Pour une utilisation ad hoc, incorporez son code à l’aide de l' [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_fit_poly_fl=(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
        '    # if x column exists check whether its a time column. If so, convert it to numeric seconds, else take it as is. If there is no x column creates sequential numbers\n'
        '    if x_col == "":\n'
        '       x = np.arange(len(y))\n'
        '    else:\n'
        '       if x_istime:\n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))/(1e9*60) #convert ticks to minutes\n'
        '           x = x - x.min()\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Fit 5th order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_fl('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Pour une utilisation permanente, utilisez la [fonction. Create](../management/create-function.md).  La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Installation unique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fit a polynomial of a specified degree to a series")
series_fit_poly_fl(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=false)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
        '    # if x column exists check whether its a time column. If so, convert it to numeric seconds, else take it as is. If there is no x column creates sequential numbers\n'
        '    if x_col == "":\n'
        '       x = np.arange(len(y))\n'
        '    else:\n'
        '       if x_istime:\n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))/(1e9*60) #convert ticks to minutes\n'
        '           x = x - x.min()\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Utilisation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Fit 5th order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_fl('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

---

:::image type="content" source="images/series-fit-poly-fl/usage-example.png" alt-text="Graphique représentant 5e ordre polynomial pour une série chronologique régulière" border="false":::

## <a name="additional-examples"></a>Exemples supplémentaires

Les exemples suivants supposent que la fonction est déjà installée :

1. Tester des séries chronologiques irrégulières (espacées de manière inégale)
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    //
    //  Test irregular (unevenly spaced) time series
    //
    let max_t = datetime(2016-09-03);
    demo_make_series1
    | where TimeStamp between ((max_t-2d)..max_t)
    | summarize num=count() by bin(TimeStamp, 5m), OsVer
    | order by TimeStamp asc
    | where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create unevenly spaced time series
    | summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
    | extend fnum = dynamic(null), coeff=dynamic(null)
    | invoke series_fit_poly_fl('num', 'fnum', 'coeff', 8, 'TimeStamp', True)
    | render timechart with(ycolumns=num, fnum)
    ```
    
    :::image type="content" source="images/series-fit-poly-fl/irregular-time-series.png" alt-text="Graphique présentant la valeur polynomiale 8 pour une série chronologique irrégulière" border="false":::

1. 5e ordre polynomial avec bruit sur les axes x & y

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    //
    // 5th order polynomial with noise on x & y axes
    //
    range x from 1 to 200 step 1
    | project x = rand()*5 - 2.3
    | extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
    | extend y = y + (rand() - 0.5)*0.5*y
    | order by x asc 
    | summarize x=make_list(x), y=make_list(y)
    | extend y_fit = dynamic(null), coeff=dynamic(null)
    | invoke series_fit_poly_fl('y', 'y_fit', 'coeff', 5, 'x')
    |fork (project-away coeff) (project coeff | mv-expand coeff)
    | render linechart
    ```
        
    :::image type="content" source="images/series-fit-poly-fl/fifth-order-noise.png" alt-text="Graphique de la taille du cinquième ordre polynomial avec le bruit sur les axes x & y":::
       
    :::image type="content" source="images/series-fit-poly-fl/fifth-order-noise-table.png" alt-text="Coefficients de la 5e ordre polynomial avec le bruit" border="false":::
