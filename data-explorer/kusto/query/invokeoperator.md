---
title: opérateur d’appel-Azure Explorateur de données
description: Cet article décrit l’opérateur d’appel dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1114952cdafe04284e93815d11c160455416b87c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248971"
---
# <a name="invoke-operator"></a>invoke, opérateur

Appelle une expression lambda qui reçoit la source de `invoke` comme argument de paramètre tabulaire.

```kusto
T | invoke foo(param1, param2)
```

> [!NOTE]
> Pour plus d’informations sur la façon de déclarer des expressions lambda pouvant accepter des arguments tabulaires, consultez [instructions Let](./letstatement.md) .
 
## <a name="syntax"></a>Syntaxe

`T | invoke`*fonction* `(` [*param1* `,` *param2*]`)`

## <a name="arguments"></a>Arguments

* *T*: source tabulaire.
* *Function*: nom de l’expression lambda ou du nom de fonction à évaluer.
* *param1*, *param2* ... : arguments lambda supplémentaires.

## <a name="returns"></a>Retours

Retourne le résultat de l’expression évaluée.

## <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser l' `invoke` opérateur pour appeler une expression lambda :

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
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
