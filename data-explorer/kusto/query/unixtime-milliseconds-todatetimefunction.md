---
title: unixtime_milliseconds_todatetime() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit unixtime_milliseconds_todatetime() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: ab229d78d2a9ff5a7e50ecefe027824488578b12
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505251"
---
# <a name="unixtime_milliseconds_todatetime"></a>unixtime_milliseconds_todatetime()

Convertit les millisecondes unix-époques en date d’utC.

**Syntaxe**

`unixtime_milliseconds_todatetime(*milliseconds*)`

**Arguments**

* *millisecondes*: Un nombre réel représente l’époque de l’époque en millisecondes. `Datetime`qui se produit avant l’heure de l’époque (1970-01-01 00:00:00) a une valeur négative timetamp.

**Retourne**

Si la conversion est réussie, le résultat sera une valeur [de date.](./scalar-data-types/datetime.md) Si la conversion n’est pas réussie, le résultat sera nul.

**Voir aussi**

* Convertir les secondes unix-époque à l’heure de date UTC en utilisant [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md).
* Convertir les microsecondes unix-epoch en date d’utC à [l’aide de unixtime_microseconds_todatetime).](unixtime-microseconds-todatetimefunction.md)
* Convertir les nanosecondes unix-epoch en date d’utC à [l’aide de unixtime_nanoseconds_todatetime).](unixtime-nanoseconds-todatetimefunction.md)

**Exemple**

```kusto
print date_time = unixtime_milliseconds_todatetime(1546300800000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|