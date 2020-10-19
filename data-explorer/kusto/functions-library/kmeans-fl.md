---
title: kmeans_fl ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur kmeans_fl () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 10/18/2020
ms.openlocfilehash: b56aefd94ed58b8731ea6237f131b4da74d4f7be
ms.sourcegitcommit: 88923cfb2495dbf10b62774ab2370b59681578b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92184136"
---
# <a name="kmeans_fl"></a>kmeans_fl ()

La fonction `kmeans_fl()` clusterizes un DataSet à l’aide de l' [algorithme k-signifiant](https://en.wikipedia.org/wiki/K-means_clustering).

> [!NOTE]
> * `kmeans_fl()` est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md).
> * Cette fonction contient python inline et nécessite l' [activation du plug-in Python ()](../query/pythonplugin.md#enable-the-plugin) sur le cluster. Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`T | invoke kmeans_fl(`*k* `,` *features_cols* `,` *cluster_col*`)`

## <a name="arguments"></a>Arguments

* *k*: nombre requis de clusters.
* *features_cols*: tableau dynamique contenant les noms des colonnes de fonctionnalités utilisées pour le clustering.
* *cluster_col*: nom de la colonne qui stocke l’ID de cluster de sortie pour chaque enregistrement.

## <a name="usage"></a>Usage

`kmeans_fl()` est une [fonction tabulaire](../query/functions/user-defined-functions.md#tabular-function) définie par l’utilisateur à appliquer à l’aide de l' [opérateur Invoke](../query/invokeoperator.md). Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données. Il existe deux options d’utilisation : une utilisation ad hoc et une utilisation permanente. Consultez les onglets ci-dessous pour obtenir des exemples.

# <a name="ad-hoc"></a>[Ad hoc](#tab/adhoc)

Pour une utilisation ad hoc, incorporez le code à l’aide de l' [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let kmeans_fl=(tbl:(*), k:int, features:dynamic, cluster_col:string)
{
    let kwargs = pack('k', k, 'features', features, 'cluster_col', cluster_col);
    let code =
        '\n'
        'from sklearn.cluster import KMeans\n'
        '\n'
        'k = kargs["k"]\n'
        'features = kargs["features"]\n'
        'cluster_col = kargs["cluster_col"]\n'
        '\n'
        'km = KMeans(n_clusters=k)\n'
        'df1 = df[features]\n'
        'km.fit(df1)\n'
        'result = df\n'
        'result[cluster_col] = km.labels_\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
};
//
// Clusterize room occupancy from sensors measurements.
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature, Humidity, Light, and CO2.
//
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| sample 10
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Pour une utilisation permanente, utilisez la [fonction. Create](../management/create-function.md). La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Installation unique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "K-Means clustering")
kmeans_fl(tbl:(*), k:int, features:dynamic, cluster_col:string)
{
    let kwargs = pack('k', k, 'features', features, 'cluster_col', cluster_col);
    let code =
        '\n'
        'from sklearn.cluster import KMeans\n'
        '\n'
        'k = kargs["k"]\n'
        'features = kargs["features"]\n'
        'cluster_col = kargs["cluster_col"]\n'
        '\n'
        'km = KMeans(n_clusters=k)\n'
        'df1 = df[features]\n'
        'km.fit(df1)\n'
        'result = df\n'
        'result[cluster_col] = km.labels_\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Usage

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Clusterize room occupancy from sensors measurements.
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature, Humidity, Light, and CO2.
//
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| sample 10
```

---

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
Timestamp                   Temperature Humidity    Light  CO2         HumidityRatio Occupancy Test  cluster_id
2015-02-02 14:38:00.0000000 23.64       27.1        473    908.8       0.00489763    TRUE      TRUE  1
2015-02-03 01:47:00.0000000 20.575      22.125      0      446         0.00330878    FALSE     TRUE  0
2015-02-10 08:47:00.0000000 20.42666667 33.56       405    494.3333333 0.004986493   TRUE      FALSE 4
2015-02-10 09:15:00.0000000 20.85666667 35.09666667 433    665.3333333 0.005358055   TRUE      FALSE 4
2015-02-11 16:13:00.0000000 21.89       30.0225     429    771.75      0.004879358   TRUE      TRUE  4
2015-02-13 14:06:00.0000000 23.4175     26.5225     608    599.75      0.004728116   TRUE      TRUE  4
2015-02-13 23:09:00.0000000 20.13333333 32.2        0      502.6666667 0.004696278   FALSE     TRUE  0
2015-02-15 18:30:00.0000000 20.5        32.79       0      666.5       0.004893459   FALSE     TRUE  3
2015-02-17 13:43:00.0000000 21.7        33.9        454    1167        0.005450924   TRUE      TRUE  1
2015-02-17 18:17:00.0000000 22.025      34.2225     0      1538.25     0.005614538   FALSE     TRUE  2
```

Extrayez le centroïdes et la taille de chaque cluster, la fonction étant déjà installée :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
OccupancyDetection
| extend cluster_id=double(null)
| invoke kmeans_fl(5, pack_array("Temperature", "Humidity", "Light", "CO2", "HumidityRatio"), "cluster_id")
| summarize Temperature=avg(Temperature), Humidity=avg(Humidity), Light=avg(Light), CO2=avg(CO2), HumidityRatio=avg(HumidityRatio), num=count() by cluster_id
| order by num

```

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
cluster_id  Temperature        Humidity            Light            CO2                HumidityRatio        num
0           20.3507186863278   27.1521395395781    10.1995789883291 486.804272186974   0.00400132147662714  11124
4           20.9247315268427   28.7971160082823    20.7311894656536 748.965771574469   0.00440412568299058  3063
1           22.0284137970445   27.8953334469889    481.872136037748 1020.70779349773   0.00456692559904535  2514
3           22.0344177115763   25.1151053429273    462.358969056434 656.310608696507   0.00411782436443015  2176
2           21.4091216082295   31.8363099552939    174.614816229606 1482.05062388414   0.00504573022875817  1683
```
