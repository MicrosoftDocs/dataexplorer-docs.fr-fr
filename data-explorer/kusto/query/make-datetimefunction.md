---
title: make_datetime ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit make_datetime () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 09a199e907f8e09a845fa037129330a2b660187e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249838"
---
# <a name="make_datetime"></a>make_datetime()

Crée une valeur scalaire [DateTime](./scalar-data-types/datetime.md) à partir de la date et de l’heure spécifiées.

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

## <a name="syntax"></a>Syntaxe

`make_datetime(`*année*,*mois*,*jour*`)`

`make_datetime(`*année*,*mois*,*jour*,*heure*,*minute*`)`

`make_datetime(`*année*,*mois*,*jour*,*heure*,*minute*,*seconde*`)`

## <a name="arguments"></a>Arguments

* *year*: Year (valeur entière comprise entre 0 et 9999)
* *Month*: month (valeur entière, comprise entre 1 et 12)
* *Day*: Day (valeur entière comprise entre 1 et 28-31)
* *heure*: heure (valeur entière comprise entre 0 et 23)
* *minute*: minute (valeur entière comprise entre 0 et 59)
* *seconde*: seconde (valeur réelle, comprise entre 0 et 59,9999999)

## <a name="returns"></a>Retours

Si la création réussit, le résultat sera une valeur [DateTime](./scalar-data-types/datetime.md) ; sinon, le résultat sera null.
 
## <a name="example"></a>Exemple

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

