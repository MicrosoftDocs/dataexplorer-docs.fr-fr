---
title: series_pearson_correlation ()-Azure Explorateur de données
description: Cet article décrit series_pearson_correlation () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: 9187c10ad62b4d925bf6211e64657fba5ae17b63
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372504"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

Calcule le coefficient de corrélation de Pearson de deux entrées de série numérique.

Consultez : [coefficient de corrélation de Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient).

**Syntaxe**

`series_pearson_correlation(`*Series1* `,` *Series2*`)`

**Arguments**

* *Series1, Series2*: entrez des tableaux numériques pour le calcul du coefficient de corrélation. Tous les arguments doivent être des tableaux dynamiques de même longueur. 

**Retourne**

Coefficient de corrélation de Pearson calculé entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit un `null` résultat.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1, 2, 3, 4, 5]|[2, 4, 6, 8, 10]|1|
