---
title: datetime_add() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit datetime_add() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ead7e0ae5c4dee94930afe1b20c4d5b99e2b4664
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516454"
---
# <a name="datetime_add"></a>datetime_add()

Calcule une nouvelle [date d’une](./scalar-data-types/datetime.md) date spécifiée multipliée par un montant spécifié, ajouté à une [date spécifiée](./scalar-data-types/datetime.md).

**Syntaxe**

`datetime_add(`*period*`,`*date de la*`,`date du montant*de la* période`)`

**Arguments**

* `period`: [chaîne](./scalar-data-types/string.md). 
* `amount`: [intégrer](./scalar-data-types/int.md).
* `datetime`: valeur [d’heure de la date.](./scalar-data-types/datetime.md)

Valeurs *d’époque*possibles : 
- Year
- Quarter
- Month
- Week
- jour
- Heure
- Minute
- Seconde
- Milliseconde
- Microseconde
- Nanoseconde

**Retourne**

Une date après un certain intervalle d’heure/date a été ajoutée.

**Exemples**

```kusto
print  year = datetime_add('year',1,make_datetime(2017,1,1)),
quarter = datetime_add('quarter',1,make_datetime(2017,1,1)),
month = datetime_add('month',1,make_datetime(2017,1,1)),
week = datetime_add('week',1,make_datetime(2017,1,1)),
day = datetime_add('day',1,make_datetime(2017,1,1)),
hour = datetime_add('hour',1,make_datetime(2017,1,1)),
minute = datetime_add('minute',1,make_datetime(2017,1,1)),
second = datetime_add('second',1,make_datetime(2017,1,1))

```

|year|quarter|month|week|day|hour|minute|second|
|---|---|---|---|---|---|---|---|
|2018-01-01 00:00:00.0000000|2017-04-01 00:00:00.0000000|2017-02-01 00:00:00.0000000|2017-01-08 00:00:00.0000000|2017-01-02 00:00:00.0000000|2017-01-01 01:00:00.0000000|2017-01-01 00:01:00.0000000|2017-01-01 00:00:01.0000000|






