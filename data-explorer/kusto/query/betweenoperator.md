---
title: between, opérateur-Explorateur de données Azure
description: Cet article décrit l’opérateur Between dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 8bb2049c7bc7c81092eb137c820f650bf88abc4e
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512976"
---
# <a name="between-operator"></a>between, opérateur

établit une correspondance avec l’entrée à l’intérieur de la plage inclusive.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between` peut fonctionner sur n’importe quelle expression numérique, DateTime ou TimeSpan.
 
## <a name="syntax"></a>Syntaxe

*T* `|` `where` *expr* `between` `(` *leftRange* ` .. ` *rightRange*`)`   
 
Si l’expression *expr* est DateTime, une autre syntaxe de sucre syntaxique est fournie :

*T* `|` `where` *expr* `between` `(` *leftRangeDateTime* ` .. ` *rightRangeTimespan*`)`   

## <a name="arguments"></a>Arguments

* *T* -l’entrée tabulaire dont les enregistrements doivent être mis en correspondance.
* *expr* -l’expression à filtrer.
* *leftRange* -expression de la plage gauche (inclusive).
* *rightRange* -expression de la plage droite (inclusive).

## <a name="returns"></a>Retours

Les lignes dans *T* pour lesquelles le prédicat de (*expr*  >=  *leftRange* et *expr*  <=  *rightRange*) prend la valeur `true` .

## <a name="examples"></a>Exemples  

**Filtrage des valeurs numériques à l’aide de l’opérateur’between'**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1
| where x between (50 .. 55)
```

|x|
|---|
|50|
|51|
|52|
|53|
|54|
|55|

**Filtrage de DateTime à l’aide de l’opérateur’between'**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Count|
|---|
|476|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|Count|
|---|
|476|
