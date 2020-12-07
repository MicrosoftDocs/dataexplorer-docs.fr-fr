---
title: Opérateur between – Azure Data Explorer
description: Cet article décrit l’opérateur between dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 8bb2049c7bc7c81092eb137c820f650bf88abc4e
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512976"
---
# <a name="between-operator"></a>between, opérateur

établit une correspondance avec l’entrée à l’intérieur de la plage inclusive.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

L’opérateur `between` peut fonctionner sur n’importe quelle expression numérique, datetime ou timespan.
 
## <a name="syntax"></a>Syntaxe

*T* `|` `where` *expr* `between` `(`*leftRange*` .. `*rightRange*`)`   
 
Si l’expression *expr* est une valeur de datetime, une autre syntaxe de sucre syntaxique est fournie :

*T* `|` `where` *expr* `between` `(`*leftRangeDateTime*` .. `*rightRangeTimespan*`)`   

## <a name="arguments"></a>Arguments

* *T* : entrée tabulaire dont les enregistrements doivent être mis en correspondance.
* *Expr* : expression à filtrer.
* *leftRange* : expression de la plage gauche (inclusive).
* *rightRange* : expression de la plage droite (inclusive).

## <a name="returns"></a>Retours

Lignes de *T* pour lesquelles le prédicat de (*expr* >= *leftRange* et *expr* <= *rightRange*) prend la valeur `true`.

## <a name="examples"></a>Exemples  

**Filtrage de valeurs numériques à l’aide de l’opérateur « between »**  

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

**Filtrage de valeur de datetime à l’aide de l’opérateur « between »**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Nombre|
|---|
|476|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|Nombre|
|---|
|476|
