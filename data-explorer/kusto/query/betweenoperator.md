---
title: entre l’opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit entre l’opérateur d’Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 336d24edbcd7574f0c4b6375b4b09014b38d10ec
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517848"
---
# <a name="between-operator"></a>between, opérateur

établit une correspondance avec l’entrée à l’intérieur de la plage inclusive.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between`peut fonctionner selon n’importe quelle expression numérique, date ou timespan.
 
**Syntaxe**

*T* `|` T `where` *expr* `between` *leftRange*` .. `*rightRange* leftRange rightRange `(``)`   
 
Si *l’expression expr* est datetime - une autre syntaxe syntaxe de sucre syntaxe est fournie :

*T* `|` T `where` *expr* `between` *leftRangeDateTime*` .. `*rightRangeTimespan* leftRangeDateTime rightRangeTimespan `(``)`   

**Arguments**

* *T* - L’entrée tabulaire dont les dossiers doivent être appariés.
* *expr* - l’expression pour filtrer.
* *gaucheRange* - expression de la gamme gauche (inclusive).
* *rightRange* - expression de la bonne plage (inclusive).

**Retourne**

Lignes en *T* pour lesquelles le prédicat de (*expr* >= *leftRange* et *expr* <= *rightRange*) évalue à `true`.

**Exemples**  

**Filtrer les valeurs numériques à l’aide d’un opérateur « entre »**  

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

**Filtrer l’heure de la date à l’aide d’un opérateur « entre deux »**  


```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Count|
|---|
|476|


```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|Count|
|---|
|476|