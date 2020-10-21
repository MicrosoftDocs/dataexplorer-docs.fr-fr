---
title: series_pearson_correlation ()-Azure Explorateur de données
description: Cet article décrit series_pearson_correlation () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: aa75e3cb2f2aefbc50c148486cb687841408e1d4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245955"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

Calcule le coefficient de corrélation de Pearson de deux entrées de série numérique.

Consultez : [coefficient de corrélation de Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient).

## <a name="syntax"></a>Syntaxe

`series_pearson_correlation(`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Arguments

* *Series1, Series2*: entrez des tableaux numériques pour le calcul du coefficient de corrélation. Tous les arguments doivent être des tableaux dynamiques de même longueur. 

## <a name="returns"></a>Retours

Coefficient de corrélation de Pearson calculé entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit un `null` résultat.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1, 2, 3, 4, 5]|[2, 4, 6, 8, 10]|1|
