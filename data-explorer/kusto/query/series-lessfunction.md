---
title: series_less ()-Azure Explorateur de données
description: Cet article décrit series_less () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ccf5638c2393ca4fbd63474d93c60d96972580fd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251538"
---
# <a name="series_less"></a>series_less()

Calcule l’opération logique au niveau de l’élément ( `<` ) de deux entrées de série numérique.

## <a name="syntax"></a>Syntaxe

`series_less (`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Arguments

* *Series1, Series2*: les tableaux numériques d’entrée doivent être comparés par élément. Tous les arguments doivent être des tableaux dynamiques. 

## <a name="returns"></a>Retours

Tableau dynamique de valeurs booléennes contenant l’opération de logique d’élément calculé moins importante entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit une `null` valeur d’élément.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_s2 = series_less(s1, s2)
```

|s1|s2|s1_less_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[true, false, false]|

## <a name="see-also"></a>Voir aussi

Pour les comparaisons de statistiques de série entières, consultez :
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
