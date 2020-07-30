---
title: unixtime_nanoseconds_todatetime ()-Azure Explorateur de données
description: Cet article décrit unixtime_nanoseconds_todatetime () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 0786863589ece863278f9cadec79c9c86a4117eb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350602"
---
# <a name="unixtime_nanoseconds_todatetime"></a>unixtime_nanoseconds_todatetime()

Convertit les nanosecondes UNIX-époques en heure UTC.

## <a name="syntax"></a>Syntaxe

`unixtime_nanoseconds_todatetime(*nanoseconds*)`

## <a name="arguments"></a>Arguments

* *nanosecondes*: un nombre réel représente l’horodatage de l’époque en nanosecondes. `Datetime`Cela se produit avant l’heure de l’époque (1970-01-01 00:00:00) a une valeur d’horodatage négative.

## <a name="returns"></a>Retourne

Si la conversion réussit, le résultat sera une valeur [DateTime](./scalar-data-types/datetime.md) . Si la conversion échoue, le résultat sera null.

**Voir aussi**

* Convertit UNIX-époque des secondes en date/heure UTC à l’aide de [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Convertit UNIX-époque des millisecondes en date/heure UTC à l’aide de [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Convertit les microsecondes UNIX-époques en date et heure UTC à l’aide de [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md).

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_nanoseconds_todatetime(1546300800000000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
