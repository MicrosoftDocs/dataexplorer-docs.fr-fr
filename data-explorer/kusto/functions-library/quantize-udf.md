---
title: quantize_udf ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur quantize_udf () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 19d35e70ddd47b1b0390778a0e775a147235c587
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557859"
---
# <a name="quantize_udf"></a>quantize_udf ()


Les `quantize_udf()` colonnes de métriques des emplacements de fonction. Il quantifie les colonnes métriques en étiquettes catégoriques, en fonction de l’algorithme K-signifiant.

> [!NOTE]
>* Cette fonction contient python inline et nécessite l' [activation du plug-in Python ()](../query/pythonplugin.md#enable-the-plugin) sur le cluster.
>* Cette fonction est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md). Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`T | invoke quantize_udf(`*num_bins* `,` *in_cols* `,` *out_cols* `,` *étiquettes*`)`

## <a name="arguments"></a>Arguments

* *num_bins*: nombre requis d’emplacements.
* *in_cols*: tableau dynamique contenant les noms des colonnes à quantifier.
* *out_cols*: tableau dynamique contenant les noms des colonnes de sortie respectives pour les valeurs Binned (.
* *étiquettes*: tableau dynamique contenant les noms d’étiquette. Ce paramètre est facultatif. Si les *étiquettes* ne sont pas fournies, les plages de l’emplacement sont utilisées.

## <a name="usage"></a>Usage

* `quantize_udf()` est une fonction définie par l’utilisateur. Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données :
    * Pour une utilisation ad hoc, incorporez son code à l’aide de l' [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.
    * Pour une utilisation récurrente, conservez-la à l’aide de la [fonction. Create](../management/create-function.md). <br>
        La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).
* `quantize_udf()` est une [fonction tabulaire](../query/functions/user-defined-functions.md#tabular-function), à appliquer à l’aide de l' [opérateur Invoke](../query/invokeoperator.md)

# <a name="ad-hoc-usage"></a>[Utilisation ad hoc](#tab/adhoc)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let qunatize_udf=(tbl:(*), num_bins:int, in_cols:dynamic, out_cols:dynamic, labels:dynamic=dynamic(null))
{
    let kwargs = pack('num_bins', num_bins, 'in_cols', in_cols, 'out_cols', out_cols, 'labels', labels);
    let code =
        '\n'
        'from sklearn.preprocessing import KBinsDiscretizer\n'
        '\n'
        'num_bins = kargs["num_bins"]\n'
        'in_cols = kargs["in_cols"]\n'
        'out_cols = kargs["out_cols"]\n'
        'labels = kargs["labels"]\n'
        '\n'
        'result = df\n'
        'binner = KBinsDiscretizer(n_bins=num_bins, encode="ordinal", strategy="kmeans")\n'
        'df_in = df[in_cols]\n'
        'bdata = binner.fit_transform(df_in)\n'
        'if labels is None:\n'
        '    for i in range(len(out_cols)):    # loop on each column and convert it to binned labels\n'
        '        ii = np.round(binner.bin_edges_[i], 3)\n'
        '        labels = [str(ii[j-1]) + \'-\' + str(ii[j]) for j in range(1, num_bins+1)]\n'
        '        result.loc[:,out_cols[i]] = np.take(labels, bdata[:, i].astype(int))\n'
        'else:\n'
        '    result[out_cols] = np.take(labels, bdata.astype(int))\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
};
//
union 
(range x from 1 to 5 step 1),
(range x from 10 to 15 step 1),
(range x from 20 to 25 step 1)
| extend x_label='', x_bin=''
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_label'), pack_array('Low', 'Med', 'High'))
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_bin'), dynamic(null))
```

# <a name="persistent-usage"></a>[Utilisation permanente](#tab/persistent)

* **Installation unique**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "Binning metric columns")
qunatize_udf(tbl:(*), num_bins:int, in_cols:dynamic, out_cols:dynamic, labels:dynamic)
{
    let kwargs = pack('num_bins', num_bins, 'in_cols', in_cols, 'out_cols', out_cols, 'labels', labels);
    let code =
        '\n'
        'from sklearn.preprocessing import KBinsDiscretizer\n'
        '\n'
        'num_bins = kargs["num_bins"]\n'
        'in_cols = kargs["in_cols"]\n'
        'out_cols = kargs["out_cols"]\n'
        'labels = kargs["labels"]\n'
        '\n'
        'result = df\n'
        'binner = KBinsDiscretizer(n_bins=num_bins, encode="ordinal", strategy="kmeans")\n'
        'df_in = df[in_cols]\n'
        'bdata = binner.fit_transform(df_in)\n'
        'if labels is None:\n'
        '    for i in range(len(out_cols)):    # loop on each column and convert it to binned labels\n'
        '        ii = np.round(binner.bin_edges_[i], 3)\n'
        '        labels = [str(ii[j-1]) + \'-\' + str(ii[j]) for j in range(1, num_bins+1)]\n'
        '        result.loc[:,out_cols[i]] = np.take(labels, bdata[:, i].astype(int))\n'
        'else:\n'
        '    result[out_cols] = np.take(labels, bdata.astype(int))\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

* **Utilisation**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
union 
(range x from 1 to 5 step 1),
(range x from 10 to 15 step 1),
(range x from 20 to 25 step 1)
| extend x_label='', x_bin=''
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_label'), pack_array('Low', 'Med', 'High'))
| invoke qunatize_udf(3, pack_array('x'), pack_array('x_bin'), dynamic(null))
```

---

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
x    x_label    x_bin
1    Low        1.0-7.75
2    Low        1.0-7.75
3    Low        1.0-7.75
4    Low        1.0-7.75
5    Low        1.0-7.75
20   High       17.5-25.0
21   High       17.5-25.0
22   High       17.5-25.0
23   High       17.5-25.0
24   High       17.5-25.0
25   High       17.5-25.0
10   Med        7.75-17.5
11   Med        7.75-17.5
12   Med        7.75-17.5
13   Med        7.75-17.5
14   Med        7.75-17.5
15   Med        7.75-17.5
```
