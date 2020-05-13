---
title: unixtime_milliseconds_todatetime ()-Azure Explorateur de données
description: Cet article décrit unixtime_milliseconds_todatetime () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 26f1eb901798a28996c8cdc148fe68a71fb9d2b8
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370282"
---
# <a name="unixtime_milliseconds_todatetime"></a>unixtime_milliseconds_todatetime()

Convertit les millisecondes d’époque UNIX en date/heure UTC.

**Syntaxe**

`unixtime_milliseconds_todatetime(*milliseconds*)`

**Arguments**

* *millisecondes*: un nombre réel représente l’horodatage de l’époque en millisecondes. `Datetime`Cela se produit avant l’heure de l’époque (1970-01-01 00:00:00) a une valeur d’horodatage négative.

**Retourne**

Si la conversion réussit, le résultat sera une valeur [DateTime](./scalar-data-types/datetime.md) . Si la conversion échoue, le résultat sera null.

**Voir aussi**

* Convertit UNIX-époque des secondes en date/heure UTC à l’aide de [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Convertit les microsecondes UNIX-époques en date et heure UTC à l’aide de [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md).
* Convertir les nanosecondes UNIX-époques en heure UTC à l’aide de [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md).

**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_milliseconds_todatetime(1546300800000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
