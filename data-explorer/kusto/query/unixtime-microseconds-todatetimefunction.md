---
title: unixtime_microseconds_todatetime ()-Azure Explorateur de données
description: Cet article décrit unixtime_microseconds_todatetime () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 713cf936878d0fa311d4637f61881f0481229617
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245714"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

Convertit les microsecondes UNIX-époques en heure UTC.

## <a name="syntax"></a>Syntaxe

`unixtime_microseconds_todatetime(*microseconds*)`

## <a name="arguments"></a>Arguments

* *microsecondes*: un nombre réel représente l’horodatage de l’époque en microsecondes. `Datetime` Cela se produit avant l’heure de l’époque (1970-01-01 00:00:00) a une valeur d’horodatage négative.

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat sera une valeur [DateTime](./scalar-data-types/datetime.md) . Si la conversion échoue, le résultat sera null.

## <a name="see-also"></a>Voir aussi

* Convertit UNIX-époque des secondes en date/heure UTC à l’aide de [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Convertit UNIX-époque des millisecondes en date/heure UTC à l’aide de [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Convertir les nanosecondes UNIX-époques en heure UTC à l’aide de [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md).

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
