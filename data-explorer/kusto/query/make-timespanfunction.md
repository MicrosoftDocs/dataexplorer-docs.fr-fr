---
title: make_timespan ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit make_timespan () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 93b8ed12bcfb83c39964f820e61f928357af62c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253186"
---
# <a name="make_timespan"></a>make_timespan()

Crée une valeur scalaire [TimeSpan](./scalar-data-types/timespan.md) à partir de la période spécifiée.

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

## <a name="syntax"></a>Syntaxe

`make_timespan(`*heure*,*minute*`)`

`make_timespan(`*heure*,*minute*,*seconde*`)`

`make_timespan(`*jour*,*heure*,*minute*,*seconde*`)`

## <a name="arguments"></a>Arguments

* *Day*: Day (valeur entière positive)
* *heure*: heure (valeur entière comprise entre 0 et 23)
* *minute*: minute (valeur entière comprise entre 0 et 59)
* *seconde*: seconde (valeur réelle, comprise entre 0 et 59,9999999)

## <a name="returns"></a>Retours

Si la création réussit, le résultat sera une valeur [TimeSpan](./scalar-data-types/timespan.md) , sinon, le résultat sera null.
 
## <a name="example"></a>Exemple

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|intervalle de temps|
|---|
|1.12:30:55.1230000|


