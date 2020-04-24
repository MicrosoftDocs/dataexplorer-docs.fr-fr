---
title: datetime_diff() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit datetime_diff() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fd62e27ac4f9ef0ec813a311ddb2b16f0a6c9a65
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516437"
---
# <a name="datetime_diff"></a>datetime_diff()

Calcule la différence de calendrier entre deux valeurs [de date.](./scalar-data-types/datetime.md)

**Syntaxe**

`datetime_diff(`*période*`,`*datetime_1*`,`*datetime_2*`)`

**Arguments**

* `period`: `string`. 
* `datetime_1`: valeur [d’heure de la date.](./scalar-data-types/datetime.md)
* `datetime_2`: valeur [d’heure de la date.](./scalar-data-types/datetime.md)

Valeurs *d’époque*possibles : 
- Year
- Quarter
- Month
- Week
- jour
- Heure
- Minute
- Seconde
- Milliseconde
- Microseconde
- Nanoseconde

**Retourne**

Un intégrant, qui `periods` représente une quantité de`datetime_1` - `datetime_2`dans le résultat de la soustraction ( ).

**Exemples**

```kusto
print
year = datetime_diff('year',datetime(2017-01-01),datetime(2000-12-31)),
quarter = datetime_diff('quarter',datetime(2017-07-01),datetime(2017-03-30)),
month = datetime_diff('month',datetime(2017-01-01),datetime(2015-12-30)),
week = datetime_diff('week',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
day = datetime_diff('day',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
hour = datetime_diff('hour',datetime(2017-10-31 01:00),datetime(2017-10-30 23:59)),
minute = datetime_diff('minute',datetime(2017-10-30 23:05:01),datetime(2017-10-30 23:00:59)),
second = datetime_diff('second',datetime(2017-10-30 23:00:10.100),datetime(2017-10-30 23:00:00.900)),
millisecond = datetime_diff('millisecond',datetime(2017-10-30 23:00:00.200100),datetime(2017-10-30 23:00:00.100900)),
microsecond = datetime_diff('microsecond',datetime(2017-10-30 23:00:00.1009001),datetime(2017-10-30 23:00:00.1008009)),
nanosecond = datetime_diff('nanosecond',datetime(2017-10-30 23:00:00.0000000),datetime(2017-10-30 23:00:00.0000007))
```

|year|quarter|month|week|day|hour|minute|second|milliseconde|microseconde|nanoseconde|
|---|---|---|---|---|---|---|---|---|---|---|
|17|2|13|5|29|2|5|10|100|100|-700|



