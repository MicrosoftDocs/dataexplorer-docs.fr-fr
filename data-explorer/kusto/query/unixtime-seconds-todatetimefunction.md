---
title: unixtime_seconds_todatetime ()-Azure Explorateur de données
description: Cet article décrit unixtime_seconds_todatetime () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: c0b771ca788c9cd988d3041f5ca008096eedb3af
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248372"
---
# <a name="unixtime_seconds_todatetime"></a>unixtime_seconds_todatetime()

Convertit les secondes UNIX-époques en heure UTC.

## <a name="syntax"></a>Syntaxe

`unixtime_seconds_todatetime(*seconds*)`

## <a name="arguments"></a>Arguments

* *secondes*: un nombre réel représente l’horodatage de l’époque, en secondes. `Datetime` Cela se produit avant l’heure de l’époque (1970-01-01 00:00:00) a une valeur d’horodatage négative.

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat sera une valeur [DateTime](./scalar-data-types/datetime.md) . Si la conversion échoue, le résultat sera null.

## <a name="see-also"></a>Voir aussi

* Convertit UNIX-époque des millisecondes en date/heure UTC à l’aide de [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Convertit les microsecondes UNIX-époques en date et heure UTC à l’aide de [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md).
* Convertir les nanosecondes UNIX-époques en heure UTC à l’aide de [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md).

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_seconds_todatetime(1546300800)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
