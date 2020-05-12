---
title: between, opérateur-Explorateur de données Azure
description: Cet article décrit l’opérateur Between dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ef64818c9c5e345ffb60999c97273670026be022
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227619"
---
# <a name="between-operator"></a>between, opérateur

établit une correspondance avec l’entrée à l’intérieur de la plage inclusive.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between`peut fonctionner sur n’importe quelle expression numérique, DateTime ou TimeSpan.
 
**Syntaxe**

*T* `|` `where` *expr* `between` `(` *leftRange* ` .. ` *rightRange*`)`   
 
Si l’expression *expr* est DateTime, une autre syntaxe de sucre syntaxique est fournie :

*T* `|` `where` *expr* `between` `(` *leftRangeDateTime* ` .. ` *rightRangeTimespan*`)`   

**Arguments**

* *T* -l’entrée tabulaire dont les enregistrements doivent être mis en correspondance.
* *expr* -l’expression à filtrer.
* *leftRange* -expression de la plage gauche (inclusive).
* *rightRange* -expression de la plage droite (inclusive).

**Retourne**

Les lignes dans *T* pour lesquelles le prédicat de (*expr*  >=  *leftRange* et *expr*  <=  *rightRange*) prend la valeur `true` .

**Exemples**  

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
