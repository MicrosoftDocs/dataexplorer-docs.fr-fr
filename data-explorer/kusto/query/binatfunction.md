---
title: bin_at ()-Azure Explorateur de données
description: Cet article décrit bin_at () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 90055f644dbf653eb65546202832f7cab834a0ac
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227590"
---
# <a name="bin_at"></a>bin_at()

Arrondit les valeurs à un « chutier » de taille fixe, avec un contrôle sur le point de départ de l’emplacement.
(Voir aussi [`bin function`](./binfunction.md) .)

**Syntaxe**

`bin_at``(` *Expression* `,` *BinSize* `, ` *FixedPoint* chutiers d’expression`)`

**Arguments**

* *Expression*: expression scalaire d’un type numérique (y compris `datetime` et `timespan` ) indiquant la valeur à arrondir.
* *Corbeille*: constante scalaire du même type que l' *expression* indiquant la taille de chaque emplacement. 
* *FixedPoint*: constante scalaire du même type que l' *expression* indiquant une valeur d' *expression* qui est un « point fixe » (autrement dit, une valeur `fixed_point` pour laquelle `bin_at(fixed_point, bin_size, fixed_point) == fixed_point` .)

**Retourne**

Le multiple le plus proche de l' *expression*de *casier* ci-dessous, décalé pour que *FixedPoint* soit traduit en lui-même.

**Exemples**

|Expression                                                                    |Résultats                           |Commentaires                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|Tous les casiers seront à midi   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|Tous les casiers seront le dimanche|


Dans l’exemple suivant, Notez que l' `"fixed point"` argument arg est retourné comme l’un des emplacements et que les autres emplacements sont alignés sur ce dernier en fonction de `bin_size` . Notez également que chaque emplacement DateTime représente l’heure de début de cet emplacement :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
