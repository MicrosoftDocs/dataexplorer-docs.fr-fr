---
title: make_datetime() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit make_datetime() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c51b99e4d668ec4a5f96cdfd72442d7bcab553a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512918"
---
# <a name="make_datetime"></a>make_datetime()

Crée une valeur scalaire [d’heure](./scalar-data-types/datetime.md) de date à partir de la date et de l’heure spécifiées.

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

**Syntaxe**

`make_datetime(`*année,**mois,**jour*`)`

`make_datetime(`*année*,*mois,**jour,**heure,**minute*`)`

`make_datetime(`*année*,*mois*,*jour,**heure,**minute*,*deuxième*`)`

**Arguments**

* *année*: année (valeur integer, de 0 à 9999)
* *mois*: mois (valeur integer, de 1 à 12)
* *jour*: jour (valeur integer, de 1 à 28-31)
* *heure*: heure (valeur integer, de 0 à 23)
* *minute*: minute (valeur integer, de 0 à 59)
* *deuxième*: deuxième (valeur réelle, de 0 à 59.99999999)

**Retourne**

Si la création est réussie, le résultat sera une valeur [datetime,](./scalar-data-types/datetime.md) sinon, le résultat sera nul.
 
**Exemple**

```kusto
print year_month_day = make_datetime(2017,10,01)
```

|year_month_day|
|---|
|2017-10-01 00:00:00.0000000|




```kusto
print year_month_day_hour_minute = make_datetime(2017,10,01,12,10)
```

|year_month_day_hour_minute|
|---|
|2017-10-01 12:10:00.0000000|




```kusto
print year_month_day_hour_minute_second = make_datetime(2017,10,01,12,11,0.1234567)
```

|year_month_day_hour_minute_second|
|---|
|2017-10-01 12:11:00.1234567|

