---
title: datetime_part ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit datetime_part () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: a901c7dd3c8d2011411b18faf2be33cc9434ea3f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252424"
---
# <a name="datetime_part"></a>datetime_part()

Extrait la partie de date demandée sous la forme d’une valeur entière.

```kusto
datetime_part("Day",datetime(2015-12-14))
```

## <a name="syntax"></a>Syntaxe

`datetime_part(`*partie* `,` *date/heure*`)`

## <a name="arguments"></a>Arguments

* `date`: `datetime`
* `part`: `string`

Les valeurs possibles sont les `part` suivantes : 
* Year (Année)
* Quarter (Trimestre)
* Month (Mois)
* week_of_year
* Jour
* DayOfYear
* Hour
* Minute
* Second
* Milliseconde
* Microseconde
* Nanoseconde

## <a name="returns"></a>Retours

Entier représentant la partie extraite.

> [!NOTE]
> `week_of_year` retourne un entier qui représente le numéro de la semaine. Le numéro de semaine est calculé à partir de la première semaine de l’année, qui est celle qui comprend le premier jeudi.

## <a name="examples"></a>Exemples

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
> `weekofyear` est une variante obsolète de la `week_of_year` partie. `weekofyear` n’était pas conforme à la norme ISO 8601 ; la première semaine de l’année a été définie en tant que semaine avec le premier mercredi de l’année.
> `week_of_year` est conforme à la norme ISO 8601 ; la première semaine de l’année est définie comme la semaine avec le premier jeudi de l’année. [Pour plus d'informations](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).