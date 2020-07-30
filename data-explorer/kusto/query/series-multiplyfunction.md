---
title: series_multiply ()-Azure Explorateur de données
description: Cet article décrit series_multiply () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 112fe4135b6ed996c798e5f03a34120e4078c623
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351384"
---
# <a name="series_multiply"></a>series_multiply()

Calcule la multiplication par élément de deux entrées de série numérique.

## <a name="syntax"></a>Syntaxe

`series_multiply(`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Arguments

* *Series1, Series2*: entrez des tableaux numériques, qui doivent être multipliés par un élément dans un résultat de tableau dynamique. Tous les arguments doivent être des tableaux dynamiques. 

## <a name="returns"></a>Retourne

Tableau dynamique d’opérations de multiplication par élément calculé entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de tailles différentes) produit une `null` valeur d’élément.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [4, 4, 4]|
|[2, 4, 8]    |[8, 4, 2]|   [16, 16, 16]|
|[3, 6, 12]   |[12, 6, 3]|  [36, 36, 36]|
