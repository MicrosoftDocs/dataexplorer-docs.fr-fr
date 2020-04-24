---
title: datetime_part() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit datetime_part () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: c64208725f0d5c49a7ea7733f8eb5a208e19225b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516369"
---
# <a name="datetime_part"></a>datetime_part()

Extrait la partie de date demandée comme valeur d’un intégreur.

```kusto
datetime_part("Day",datetime(2015-12-14))
```

**Syntaxe**

`datetime_part(`*part*`,`*date à temps partiel*`)`

**Arguments**

* `date`: `datetime`
* `part`: `string`

Valeurs possibles `part`de : 
- Year
- Quarter
- Month
- week_of_year
- jour
- DayOfYear
- Heure
- Minute
- Seconde
- Milliseconde
- Microseconde
- Nanoseconde

**Retourne**

Un intégrant représentant la partie extraite.

**Remarque**

`week_of_year`retourne un intégrant qui représente le numéro de semaine. Le nombre de semaines est calculé à partir de la première semaine d’une année, qui est celle qui comprend le premier jeudi.

**Exemples**

```kusto
let dt = datetime(2017-10-30 01:02:03.7654321); 
print 
year = datetime_part("year", dt),
quarter = datetime_part("quarter", dt),
month = datetime_part("month", dt),
weekOfYear = datetime_part("week_of_year", dt),
day = datetime_part("day", dt),
dayOfYear = datetime_part("dayOfYear", dt),
hour = datetime_part("hour", dt),
minute = datetime_part("minute", dt),
second = datetime_part("second", dt),
millisecond = datetime_part("millisecond", dt),
microsecond = datetime_part("microsecond", dt),
nanosecond = datetime_part("nanosecond", dt)

```

|year|quarter|month|weekOfYear|day|dayOfYear|hour|minute|second|milliseconde|microseconde|nanoseconde|
|---|---|---|---|---|---|---|---|---|---|---|---|
|2017|4|10|44|30|303|1|2|3|765|765432|765432100|

> [!NOTE]
> `weekofyear`est une variante `week_of_year` obsolète de la partie. `weekofyear`n’était pas conforme à l’ISO 8601; la première semaine d’une année a été définie comme la semaine avec le premier mercredi de l’année en elle.
`week_of_year`est conforme à l’ISO 8601; la première semaine d’une année est définie comme la semaine avec le premier jeudi de l’année en elle. [Pour plus d’informations](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).