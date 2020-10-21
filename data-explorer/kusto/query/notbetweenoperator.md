---
title: opérateur notBetween-Azure Explorateur de données
description: Cet article décrit l’opérateur notBetween dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7dd8f04010cbc10afa34fa8b27e6be8d31feae9d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248705"
---
# <a name="not-between-operator-between"></a>not-between, opérateur (!between)

Correspond à l’entrée qui se trouve en dehors de la plage inclusive.

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between` peut fonctionner sur n’importe quelle expression numérique, DateTime ou TimeSpan.
 
## <a name="syntax"></a>Syntaxe

*T* `|` `where` *expr* `!between` `(` *leftRange* ` .. ` *rightRange*`)`   
 
Si l’expression *expr* est DateTime, une autre syntaxe de sucre syntaxique est fournie :

*T* `|` `where` *expr* `!between` `(` *leftRangeDateTime* ` .. ` *rightRangeTimespan*`)`   

## <a name="arguments"></a>Arguments

* *T* -l’entrée tabulaire dont les enregistrements doivent être mis en correspondance.
* *expr* -l’expression à filtrer.
* *leftRange* -expression de la plage gauche (inclusive).
* *rightRange* -expression de la plage droite (inclusive).

## <a name="returns"></a>Retours

Les lignes dans *T* pour lesquelles le prédicat de (*expr*  <  *leftRange* ou *expr*  >  *rightRange*) prend la valeur `true` .

## <a name="examples"></a>Exemples  

**Filtrage des valeurs numériques à l’aide de l’opérateur' ! between'**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 10 step 1
| where x !between (5 .. 9)
```

|x|
|---|
|1|
|2|
|3|
|4|
|10|

**Filtrage de DateTime à l’aide de l’opérateur’between'**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Nombre|
|---|
|58590|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|Nombre|
|---|
|58590|
