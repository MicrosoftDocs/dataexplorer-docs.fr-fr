---
title: series_downsample_fl ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur series_downsample_fl () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2020
ms.openlocfilehash: 172d866a84e4718e3d95e8fa646187e79bec65f7
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321860"
---
# <a name="series_downsample_fl"></a>series_downsample_fl()


La fonction `series_downsample_fl()` [downsamples une série chronologique par un facteur entier](https://en.wikipedia.org/wiki/Downsampling_(signal_processing)#Downsampling_by_an_integer_factor). Cette fonction prend une table contenant plusieurs séries chronologiques (tableau numérique dynamique) et downsamples chaque série. La sortie contient à la fois la série la plus grossière et son tableau d’heures respectif. Pour éviter les [alias](https://en.wikipedia.org/wiki/Aliasing), la fonction applique un [filtre passe-bas](https://en.wikipedia.org/wiki/Low-pass_filter) simple sur chaque série avant le sous-échantillonnage.

> [!NOTE]
> Cette fonction est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md). Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`T | invoke series_downsample_fl(`*t_col* `,` *y_col* `,` *ds_t_col* `,` *ds_y_col* `,` *sampling_factor*`)`

## <a name="arguments"></a>Arguments

* *t_col*: nom de la colonne (de la table d’entrée) contenant l’axe de temps de la série à sous-échantillonner.
* *y_col*: nom de la colonne (de la table d’entrée) contenant la série à sous-échantillonner.
* *ds_t_col*: nom de la colonne pour stocker l’axe de temps sous-échantillonné de chaque série.
* *ds_y_col*: nom de la colonne dans laquelle stocker la série échantillonnée.
* *sampling_factor*: entier spécifiant l’échantillonnage requis.

## <a name="usage"></a>Utilisation

`series_downsample_fl()` est une [fonction tabulaire](../query/functions/user-defined-functions.md#tabular-function)définie par l’utilisateur, à appliquer à l’aide de l' [opérateur Invoke](../query/invokeoperator.md). Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données. Il existe deux options d’utilisation : une utilisation ad hoc et une utilisation permanente. Consultez les onglets ci-dessous pour obtenir des exemples.

# <a name="ad-hoc"></a>[Ad hoc](#tab/adhoc)

Pour une utilisation ad hoc, incorporez son code à l’aide de l' [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_downsample_fl=(tbl:(*), t_col:string, y_col:string, ds_t_col:string, ds_y_col:string, sampling_factor:int)
{
    tbl
    | extend _t_ = column_ifexists(t_col, dynamic(0)), _y_ = column_ifexists(y_col, dynamic(0))
    | extend _y_ = series_fir(_y_, repeat(1, sampling_factor), true, true)    //  apply a simple low pass filter before sub-sampling
    | mv-apply _t_ to typeof(DateTime), _y_ to typeof(double) on
    (extend rid=row_number()
    | where rid % sampling_factor == (sampling_factor/2+1)                    //  sub-sampling
    | summarize _t_ = make_list(_t_), _y_ = make_list(_y_))
    | extend cols = pack(ds_t_col, _t_, ds_y_col, _y_)
    | project-away _t_, _y_
    | evaluate bag_unpack(cols)
}
;
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| invoke series_downsample_fl('TimeStamp', 'num', 'coarse_TimeStamp', 'coarse_num', 4)
| render timechart with(xcolumn=coarse_TimeStamp, ycolumns=coarse_num)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Pour une utilisation permanente, utilisez [`.create function`](../management/create-function.md) . La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Installation unique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Downsampling a series by an integer factor")
series_downsample_fl(tbl:(*), t_col:string, y_col:string, ds_t_col:string, ds_y_col:string, sampling_factor:int)
{
    tbl
    | extend _t_ = column_ifexists(t_col, dynamic(0)), _y_ = column_ifexists(y_col, dynamic(0))
    | extend _y_ = series_fir(_y_, repeat(1, sampling_factor), true, true)    //  apply a simple low pass filter before sub-sampling
    | mv-apply _t_ to typeof(DateTime), _y_ to typeof(double) on
    (extend rid=row_number()
    | where rid % sampling_factor == (sampling_factor/2+1)                    //  sub-sampling
    | summarize _t_ = make_list(_t_), _y_ = make_list(_y_))
    | extend cols = pack(ds_t_col, _t_, ds_y_col, _y_)
    | project-away _t_, _y_
    | evaluate bag_unpack(cols)
}
```

### <a name="usage"></a>Utilisation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| invoke series_downsample_fl('TimeStamp', 'num', 'coarse_TimeStamp', 'coarse_num', 4)
| render timechart with(xcolumn=coarse_TimeStamp, ycolumns=coarse_num)
```

---

Série chronologique sous-échantillonnée par 4 : graphique présentant le sous- :::image type="content" source="images/series-downsample-fl/downsampling-demo.png" alt-text="échantillonnage d’une série chronologique" border="false":::

Pour référence, voici la série chronologique d’origine (avant le sous-échantillonnage) :
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| render timechart with(xcolumn=TimeStamp, ycolumns=num)
```

:::image type="content" source="images/series-downsample-fl/original-time-series.png" alt-text="Graphique présentant la série chronologique d’origine, avant le sous-échantillonnage" border="false":::

