---
title: series_metric_fl ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur series_metric_fl () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 2af82906be2cdbffdc69646a3050376e0a1af867
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389150"
---
# <a name="series_metric_fl"></a>series_metric_fl ()


La `series_metric_fl()` fonction sélectionne et récupère des séries chronologiques de métriques gérées dans Azure Explorateur de données à l’aide du système de surveillance [Prometheus](https://prometheus.io/) . Cette fonction suppose que les données stockées dans Azure Explorateur de données sont structurées suivant le [modèle de données Prometheus](https://prometheus.io/docs/concepts/data_model/). Plus précisément, chaque enregistrement contient les éléments suivants :
 * timestamp 
 * nom de la mesure 
 * valeur métrique 
 * ensemble variable d’étiquettes ( `key:value` paires)
 
 Prometheus définit une série chronologique par son nom de mesure et un ensemble distinct d’étiquettes. Vous pouvez récupérer des ensembles de séries chronologiques à l’aide du [langage de requête Prometheus (PromQL)](https://prometheus.io/docs/prometheus/latest/querying/basics/) en spécifiant le nom de la mesure et le sélecteur de série chronologique (un ensemble d’étiquettes).

> [!NOTE]
> `series_metric_fl()` est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md). Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`T | invoke series_metric_fl(`*timestamp_col* `,` *name_col* `,` *labels_col* `,` *value_col* `,` *METRIC_NAME* `,` *labels_selector* `,` *lookback* `,` *décalage*`)`

## <a name="arguments"></a>Arguments

* *timestamp_col*: nom de la colonne contenant l’horodateur.
* *name_col*: nom de la colonne contenant le nom de la mesure.
* *labels_col*: nom de la colonne contenant le dictionnaire d’étiquettes.
* *value_col*: nom de la colonne contenant la valeur de la métrique.
* *METRIC_NAME*: série chronologique à récupérer.
* *labels_selector*: chaîne du sélecteur de série chronologique, [similaire à PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/#time-series-selectors). Il s’agit d’une chaîne contenant une liste de `key:value` paires, par exemple `'key1:val1,key2:val2'` . Ce paramètre est facultatif, par défaut, une chaîne vide, ce qui signifie qu’il n’y a pas de filtrage. Notez que les expressions régulières ne sont pas prises en charge. 
* *lookback*: vecteur de plage à récupérer, [similaire à PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/#range-vector-selectors). Ce paramètre est facultatif. sa valeur par défaut est 10 minutes.
* *décalage*: décalage de l’heure actuelle à récupérer, [similaire à PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/#offset-modifier). Les données sont extraites de l’ancienneté *(décalage)-lookback* à l’ancienneté *(décalage)*. Ce paramètre est facultatif. la valeur par défaut est 0, ce qui signifie que les données sont récupérées jusqu’à maintenant ().

## <a name="usage"></a>Usage

`series_metric_fl()` est une [fonction tabulaire](../query/functions/user-defined-functions.md#tabular-function)définie par l’utilisateur, à appliquer à l’aide de l' [opérateur Invoke](../query/invokeoperator.md). Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données. Il existe deux options d’utilisation : une utilisation ad hoc et une utilisation permanente. Consultez les onglets ci-dessous pour obtenir des exemples.

# <a name="ad-hoc"></a>[Ad hoc](#tab/adhoc)

Pour une utilisation ad hoc, incorporez son code à l’aide de l' [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_metric_fl=(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Pour une utilisation permanente, utilisez [`.create function`](../management/create-function.md) . La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Installation unique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\Series", docstring = "Selecting & retrieving metrics like PromQL")
series_metric_fl(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
```

### <a name="usage"></a>Usage

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-metric-fl/disk-write-metric-10m.png" alt-text="Graphique présentant les métriques d’écriture sur le disque de plus de 10 minutes" border="false":::

## <a name="example"></a>Exemple

L’exemple suivant ne spécifie pas de sélecteur, donc toutes les métriques « écritures » sont sélectionnées. Cet exemple suppose que la fonction est déjà installée et utilise une autre syntaxe d’appel direct, en spécifiant la table d’entrée comme premier paramètre :
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels, ysplit=axes)
```
    
:::image type="content" source="images/series-metric-fl/all-disks-write-metric-10m.png" alt-text="Graphique présentant les métriques d’écriture sur le disque pour tous les disques de plus de 10 minutes" border="false":::
