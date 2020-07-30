---
title: datetime_add ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit datetime_add () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 766f0617b70e21194d731ae1cf8eabf1014265bb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348545"
---
# <a name="datetime_add"></a>datetime_add()

Calcule une nouvelle valeur DateTime à partir d’une partie de [Date](./scalar-data-types/datetime.md) spécifiée, multipliée par une quantité spécifiée, ajoutée à une valeur [DateTime](./scalar-data-types/datetime.md)spécifiée.

## <a name="syntax"></a>Syntaxe

`datetime_add(`*période* `,` *quantité* `,` *date/heure*`)`

## <a name="arguments"></a>Arguments

* `period`: [chaîne](./scalar-data-types/string.md). 
* `amount`: [entier](./scalar-data-types/int.md).
* `datetime`: valeur [DateTime](./scalar-data-types/datetime.md) .

Valeurs possibles de *period*: 
- Year (Année)
- Quarter (Trimestre)
- Month (Mois)
- Week
- jour
- Hour
- Minute
- Second
- Milliseconde
- Microseconde
- Nanoseconde

## <a name="returns"></a>Retourne

Date après l’ajout d’un intervalle de date/heure donné.

## <a name="examples"></a>Exemples

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






