---
title: series_pearson_correlation() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_pearson_correlation() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: 6454ec528e7a9e53b2feab5a7fefa1236ed80cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508107"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

Calcule le coefficient de corrélation pearson de deux entrées de série numérique.

Voir : [Coefficient de corrélation Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient).

**Syntaxe**

`series_pearson_correlation(`*Série 1* `,` *Série2*`)`

**Arguments**

* *Série 1, Série2*: Entrées de tableaux numériques pour calculer le coefficient de corrélation. Tous les arguments doivent être des tableaux dynamiques de la même longueur. 

**Retourne**

Le coefficient calculé de corrélation Pearson entre les deux intrants. Tout élément non numérique ou élément non existant (tableaux de `null` différentes tailles) donne un résultat.

**Exemple**

```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1,2,3,4,5]|[2,4,6,8,10]|1|
