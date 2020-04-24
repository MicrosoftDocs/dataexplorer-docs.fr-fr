---
title: unixtime_microseconds_todatetime() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit unixtime_microseconds_todatetime() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 6ab593049fcafeba27b8158a1c9c6a5b6fa75fe3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505268"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

Convertit les microsecondes unix-epoch en datetime UTC.

**Syntaxe**

`unixtime_microseconds_todatetime(*microseconds*)`

**Arguments**

* *microsecondes*: Un nombre réel représente l’époque de l’époque en microsecondes. `Datetime`qui se produit avant l’heure de l’époque (1970-01-01 00:00:00) a une valeur négative timetamp.

**Retourne**

Si la conversion est réussie, le résultat sera une valeur [de date.](./scalar-data-types/datetime.md) Si la conversion n’est pas réussie, le résultat sera nul.

**Voir aussi**

* Convertir les secondes unix-époque à l’heure de date UTC en utilisant [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md).
* Convertir les millisecondes unix-epoch en date d’utC à [l’aide de unixtime_milliseconds_todatetime).](unixtime-milliseconds-todatetimefunction.md)
* Convertir les nanosecondes unix-epoch en date d’utC à [l’aide de unixtime_nanoseconds_todatetime).](unixtime-nanoseconds-todatetimefunction.md)

**Exemple**

```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|