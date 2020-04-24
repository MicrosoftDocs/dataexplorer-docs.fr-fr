---
title: notbetween opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit notbetween opérateur dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: eacde679f05ff79f5ee0d223ba005217dbf5192c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512085"
---
# <a name="between-operator"></a>!entre opérateur

Correspond à l’entrée qui est en dehors de la plage inclusive.

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between`peut fonctionner selon n’importe quelle expression numérique, date ou timespan.
 
**Syntaxe**

*T* `|` T `where` *expr* `!between` *leftRange*` .. `*rightRange* leftRange rightRange `(``)`   
 
Si *l’expression expr* est datetime - une autre syntaxe syntaxe de sucre syntaxe est fournie :

*T* `|` T `where` *expr* `!between` *leftRangeDateTime*` .. `*rightRangeTimespan* leftRangeDateTime rightRangeTimespan `(``)`   

**Arguments**

* *T* - L’entrée tabulaire dont les dossiers doivent être appariés.
* *expr* - l’expression pour filtrer.
* *gaucheRange* - expression de la gamme gauche (inclusive).
* *rightRange* - expression de la gamme rihgt (inclusive).

**Retourne**

Lignes en *T* pour lesquelles le prédicat de (*expr* < *leftRange* ou *expr* > *rightRange*) évalue à `true`.

**Exemples**  

**Filtrer les valeurs numériques à l’aide d’un opérateur '!entre les deux'**  

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

**Filtrer l’heure de la date à l’aide d’un opérateur « entre deux »**  


```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Count|
|---|
|58590|


```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|Count|
|---|
|58590|