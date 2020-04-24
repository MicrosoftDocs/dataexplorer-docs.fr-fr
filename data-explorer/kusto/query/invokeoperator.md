---
title: invoquer l’opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur invoqueur dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41f19440795f4f302352a8dda5192c5c4790ea99
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513700"
---
# <a name="invoke-operator"></a>invoke, opérateur

Invoque lambda qui `invoke` reçoit la source d’argument de paramètre tabulaire.

```kusto
T | invoke foo(param1, param2)
```

**Syntaxe**

`T | invoke`*fonction*`(`[*param1* `,` *param2*]`)`

**Arguments**

* *T*: La source tabulaire.
* *fonction*: Nom de l’expression lambda ou nom de fonction à évaluer.
* *param1*, *param2* ... : arguments lambda supplémentaires.

**Retourne**

Retourne le résultat de l’expression évaluée.

**Remarques**

Voir [laisser les déclarations](./letstatement.md) pour plus de détails comment déclarer les expressions lambda qui peuvent accepter des arguments tabulaires.

**Exemple**

L’exemple suivant montre `invoke` comment utiliser l’opérateur pour appeler l’expression lambda:

```kusto
// clipped_average(): calculates percentiles limits, and then makes another 
//                    pass over the data to calculate average with values inside the percentiles
let clipped_average = (T:(x: long), lowPercentile:double, upPercentile:double)
{
   let high = toscalar(T | summarize percentiles(x, upPercentile));
   let low = toscalar(T | summarize percentiles(x, lowPercentile));
   T 
   | where x > low and x < high
   | summarize avg(x) 
};
range x from 1 to 100 step 1
| invoke clipped_average(5, 99)
```

|avg_x|
|---|
|52|
