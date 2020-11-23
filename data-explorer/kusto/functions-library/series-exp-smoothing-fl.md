---
title: series_exp_smoothing_fl ()-Azure Explorateur de données
description: Cet article décrit la fonction définie par l’utilisateur series_exp_smoothing_fl () dans Azure Explorateur de données.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: 373dd34145a644fe14ea4c077a8c55ddf428ba6d
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324905"
---
# <a name="series_exp_smoothing_fl"></a>series_exp_smoothing_fl ()

Applique un filtre de lissage exponentiel de base sur une série.

La fonction `series_exp_smoothing_fl()` prend une expression contenant un tableau numérique dynamique comme entrée et applique un filtre de [lissage exponentiel de base](https://en.wikipedia.org/wiki/Exponential_smoothing#Basic_(simple)_exponential_smoothing_(Holt_linear)) .

> [!NOTE]
> Cette fonction est une [fonction définie par l’utilisateur (UDF)](../query/functions/user-defined-functions.md). Pour plus d’informations, consultez [utilisation](#usage).

## <a name="syntax"></a>Syntaxe

`series_exp_smoothing_fl(`*y_series* `, [` *alpha*`])`
  
## <a name="arguments"></a>Arguments

* *y_series*: cellule de tableau dynamique de valeurs numériques.
* *alpha*: valeur réelle facultative dans la plage [0-1], en spécifiant le poids du dernier point par rapport au poids des points précédents (c’est-à-dire `1-alpha` ). La valeur par défaut est 0,5.

## <a name="usage"></a>Utilisation

`series_exp_smoothing_fl()` est une fonction définie par l’utilisateur. Vous pouvez incorporer son code dans votre requête ou l’installer dans votre base de données. Il existe deux options d’utilisation : une utilisation ad hoc et une utilisation permanente. Consultez les onglets ci-dessous pour obtenir des exemples.

# <a name="ad-hoc"></a>[Ad hoc](#tab/adhoc)

Pour une utilisation ad hoc, incorporez son code à l’aide d’une [instruction Let](../query/letstatement.md). Aucune autorisation n’est requise.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_exp_smoothing_fl = (y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
;
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Pour une utilisation permanente, utilisez la [fonction. Create](../management/create-function.md). La création d’une fonction nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Installation unique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Basic exponential smoothing for a series")
series_exp_smoothing_fl(y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
```

### <a name="usage"></a>Utilisation

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

---

:::image type="content" source="images/series-exp-smoothing-fl/exp-smoothing-demo.png" alt-text="Graphique présentant le lissage exponentiel de la série artificielle" border="false":::
