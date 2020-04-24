---
title: unixtime_seconds_todatetime() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit unixtime_seconds_todatetime() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 74abf66dabb7a8fc74085c695081d6a11bfdf3a3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505217"
---
# <a name="unixtime_seconds_todatetime"></a>unixtime_seconds_todatetime()

Convertit les secondes unix-époque à l’heure de date UTC.

**Syntaxe**

`unixtime_seconds_todatetime(*seconds*)`

**Arguments**

* *secondes*: Un nombre réel représente l’époque pendant quelques secondes. `Datetime`qui se produit avant l’heure de l’époque (1970-01-01 00:00:00) a une valeur négative timetamp.

**Retourne**

Si la conversion est réussie, le résultat sera une valeur [de date.](./scalar-data-types/datetime.md) Si la conversion n’est pas réussie, le résultat sera nul.

**Voir aussi**

* Convertir les millisecondes unix-epoch en date d’utC à [l’aide de unixtime_milliseconds_todatetime).](unixtime-milliseconds-todatetimefunction.md)
* Convertir les microsecondes unix-epoch en date d’utC à [l’aide de unixtime_microseconds_todatetime).](unixtime-microseconds-todatetimefunction.md)
* Convertir les nanosecondes unix-epoch en date d’utC à [l’aide de unixtime_nanoseconds_todatetime).](unixtime-nanoseconds-todatetimefunction.md)

**Exemple**

```kusto
print date_time = unixtime_seconds_todatetime(1546300800)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|