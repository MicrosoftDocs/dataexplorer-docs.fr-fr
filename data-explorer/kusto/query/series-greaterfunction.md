---
title: series_greater ()-Azure Explorateur de données
description: Cet article décrit series_greater () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 5e961e1dbcbb66ca13ee1d6fe1c04314778e07f5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242679"
---
# <a name="series_greater"></a>series_greater()

Calcule l’opération logique supérieure () au niveau `>` des éléments de deux entrées de série numérique.

## <a name="syntax"></a>Syntaxe

`series_greater (`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Arguments

* *Series1, Series2*: les tableaux numériques d’entrée doivent être comparés par élément. Tous les arguments doivent être des tableaux dynamiques. 

## <a name="returns"></a>Retours

Tableau dynamique de valeurs booléennes contenant l’opération logique par élément calculé la plus importante entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit une `null` valeur d’élément.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_s2 = series_greater(s1, s2)
```

|s1|s2|s1_greater_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[false, false, true]|

## <a name="see-also"></a>Voir aussi

Pour les comparaisons de statistiques de série entières, consultez :
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
