---
title: series_rate_fl ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur series_rate_fl () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 6678f17624225895734781f32782ed5b9bd79955
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389142"
---
# <a name="series_rate_fl"></a>series_rate_fl ()


La fonction `series_rate_fl()` calcule le taux moyen d’augmentation de métrique par seconde. Sa logique suit la fonction PromQL [rate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) . Elle doit être utilisée pour les séries chronologiques de mesures de compteur gérées dans Azure Explorateur de données par le système de surveillance [Prometheus](https://prometheus.io/) et récupérées par [series_metric_fl ()](series-metric-fl.md).

> [!NOTE]
>`series_rate_fl()` est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md). Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`T | invoke series_rate_fl(`*n_bins*`)`

`T` table renvoyée par [series_metric_fl ()](series-metric-fl.md). Son schéma comprend `(timestamp:dynamic, name:string, labels:string, value:dynamic)` .

## <a name="arguments"></a>Arguments

* *n_bins*: nombre d’emplacements pour spécifier l’intervalle entre les valeurs de mesure extraites pour le calcul du taux. La fonction calcule la différence entre l’échantillon actuel et le *n_bins* avant, puis le divise par la différence de leurs horodateurs respectifs, en secondes. Ce paramètre est facultatif, avec un emplacement par défaut. Les paramètres par défaut calculent [irate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate), la fonction de taux instantané PromQL.

## <a name="usage"></a>Usage

`series_rate_fl()` est une [fonction tabulaire](../query/functions/user-defined-functions.md#tabular-function)définie par l’utilisateur, à appliquer à l’aide de l' [opérateur Invoke](../query/invokeoperator.md). Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données. Il existe deux options d’utilisation : une utilisation ad hoc et une utilisation permanente. Consultez les onglets ci-dessous pour obtenir des exemples.

> [!NOTE]
> [series_metric_fl ()](series-metric-fl.md) est utilisé dans le cadre de la fonction et doit être installé ou incorporé.

# <a name="ad-hoc"></a>[Ad hoc](#tab/adhoc)

Pour une utilisation ad hoc, incorporez son code à l’aide de l' [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rate_fl=(tbl:(timestamp:dynamic, name:string, labels:string, value:dynamic), n_bins:int=1)
{
    tbl
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend dv = array_iff(series_greater_equals(dv, 0), dv, value)  //  handles negative difference like PromQL
    | extend rate = series_divide(dv, dt)
    | project timestamp, name, rate, labels
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Pour une utilisation permanente, utilisez [`.create function`](../management/create-function.md) . La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Installation unique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Simulate PromQL rate()")
series_rate_fl(tbl:(timestamp:dynamic, name:string, labels:string, value:dynamic), n_bins:int=1)
{
    tbl
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend dv = array_iff(series_greater_equals(dv, 0), dv, value)  //  handles negative difference like PromQL
    | extend rate = series_divide(dv, dt)
    | project timestamp, name, rate, labels
}
```

### <a name="usage"></a>Usage

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-rate-fl/all-disks-write-rate-2-bins.png" alt-text="Graphique représentant le taux par seconde de métrique d’écriture sur le disque pour tous les disques" border="false":::

## <a name="example"></a>Exemple

L’exemple suivant sélectionne le disque principal de deux hôtes et suppose que la fonction est déjà installée. Cet exemple utilise une autre syntaxe d’appel direct, en spécifiant la table d’entrée comme premier paramètre :
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_rate_fl(series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1', lookback=2h, offset=now()-datetime(2020-12-08 00:00)), n_bins=10)
| render timechart with(series=labels)
```
    
:::image type="content" source="images/series-rate-fl/main-disks-write-rate-10-bins.png" alt-text="Graphique représentant le taux par seconde de la mesure d’écriture sur le disque principal au cours des deux dernières heures avec 10 espaces vides" border="false":::
