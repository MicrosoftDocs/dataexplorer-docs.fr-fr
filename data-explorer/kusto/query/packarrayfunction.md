---
title: pack_array ()-Azure Explorateur de données
description: Cet article décrit pack_array () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ad6403a70a8f71eee05bc56789cae2d6287a6605
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346522"
---
# <a name="pack_array"></a>pack_array()

Compresse toutes les valeurs d’entrée dans un tableau dynamique.

## <a name="syntax"></a>Syntaxe

`pack_array(`*Expr1* `[` , ` *Expr2*]` )»

## <a name="arguments"></a>Arguments

* *Expr1... N*: expressions d’entrée à empaqueter dans un tableau dynamique.

## <a name="returns"></a>Retourne

Tableau dynamique qui comprend les valeurs de expr1, expr2,..., Exprn.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|Colonne1|
|---|
|[1, 2, 4]|
|[2, 4, 8]|
|[3, 6, 12]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|Colonne1|
|---|
|[1, « 2 », « 00:00:02 »]|
|[2, "4", "00:00:04"]|
|[3, "6", "00:00:06"]|
