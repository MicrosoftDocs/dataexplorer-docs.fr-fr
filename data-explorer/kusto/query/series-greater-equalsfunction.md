---
title: series_greater_equals ()-Azure Explorateur de données
description: Cet article décrit series_greater_equals () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 1f8e6e8100472ed8e68a5dc1801c282b5a48ff77
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351469"
---
# <a name="series_greater_equals"></a>series_greater_equals()

Calcule l’opération logique supérieure ou égale à l’élément ( `>=` ) de deux entrées de série numérique.

## <a name="syntax"></a>Syntaxe

`series_greater_equals (`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Arguments

* *Series1, Series2*: les tableaux numériques d’entrée doivent être comparés par élément. Tous les arguments doivent être des tableaux dynamiques. 

## <a name="returns"></a>Retourne

Tableau dynamique de valeurs booléennes contenant l’opération logique supérieure ou égale à l’élément calculé entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit une `null` valeur d’élément.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_equals_s2 = series_greater_equals(s1, s2)
```

|s1|s2|s1_greater_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[false, true, true]|

**Voir aussi**

Pour les comparaisons de statistiques de série entières, consultez :
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
