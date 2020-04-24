---
title: bin_at() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit bin_at() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 218142e1c377e6a72abde154d4576025698b2f0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517440"
---
# <a name="bin_at"></a>bin_at()

Arrondit les valeurs jusqu’à un "bin" de taille fixe, avec le contrôle sur le point de départ du bac.
(Voir [`bin function`](./binfunction.md)aussi .)

**Syntaxe**

`bin_at``(` *Expression* Expression`,` *BinSize* `, ` *FixedPoint*`)`

**Arguments**

* *Expression*: Expression scalaire d’un `datetime` `timespan`type numérique (y compris et ) indiquant la valeur à arrondir.
* *BinSize*: Une constante scalaire du même type que *l’expression* indiquant la taille de chaque bac. 
* *FixedPoint*: Une constante scalaire du même type que *l’expression* indiquant une valeur *d’expression* qui est un « point fixe » (c’est-à-dire une valeur `fixed_point` pour laquelle `bin_at(fixed_point, bin_size, fixed_point) == fixed_point`.)

**Retourne**

Le multiple le plus proche de *BinSize* ci-dessous *Expression*, décalé de sorte que *FixedPoint* sera traduit en lui-même.

**Exemples**

|Expression                                                                    |Résultats                           |Commentaires                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|Tous les bacs seront à midi   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|Tous les bacs seront le dimanche|


Dans l’exemple suivant, `"fixed point"` notez que l’arg est retourné comme l’un des `bin_size`bacs et les autres bacs sont alignés à elle en fonction de la . Notez également que chaque bac de date représente l’heure de début de ce bac :

```kusto

datatable(Date:datetime, Num:int)[
datetime(2018-02-24T15:14),3,
datetime(2018-02-23T16:14),4,
datetime(2018-02-26T15:14),5]
| summarize sum(Num) by bin_at(Date, 1d, datetime(2018-02-24 15:14:00.0000000)) 
```

|Date|sum_Num|
|---|---|
|2018-02-23 15:14:00.0000000|4|
|2018-02-24 15:14:00.0000000|3|
|2018-02-26 15:14:00.0000000|5|