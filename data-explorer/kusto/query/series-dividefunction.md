---
title: series_divide ()-Azure Explorateur de données
description: Cet article décrit series_divide () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 093f78cfc30da31474094187a786585fdcd5132b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252922"
---
# <a name="series_divide"></a>series_divide()

Calcule la division au niveau des éléments de deux entrées de série numérique.

## <a name="syntax"></a>Syntaxe

`series_divide(`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Arguments

* *Series1, Series2*: entrez des tableaux numériques, le premier étant un élément, divisé par le second en un résultat de tableau dynamique. Tous les arguments doivent être des tableaux dynamiques. 

## <a name="returns"></a>Retours

Tableau dynamique de l’opération de division par élément calculé entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit une `null` valeur d’élément.

Remarque : la série de résultats est de type double, même si les entrées sont des entiers. La division par zéro suit la double division par zéro (par exemple, 2/0 donne double (+ INF)).

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [0.25, 1.0, 4.0]|
|[2, 4, 8]    |[8, 4, 2]|   [0.25, 1.0, 4.0]|
|[3, 6, 12]   |[12, 6, 3]|  [0.25, 1.0, 4.0]|
