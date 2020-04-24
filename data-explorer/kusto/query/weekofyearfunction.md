---
title: week_of_year() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit week_of_year() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 1c3702165f01ab321f80bad900e59968092be2ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504537"
---
# <a name="week_of_year"></a>week_of_year()

Retourne un intégrant qui représente le numéro de semaine. Le numéro de la semaine est calculé à partir de la première semaine d’une année, qui est celui qui comprend le premier jeudi, selon [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).

```kusto
week_of_year(datetime("2015-12-14"))
```

**Syntaxe**

`week_of_year(`*a_date*`)`

**Arguments**

* `a_date` : une `datetime`.

**Retourne**

`week number`- Le numéro de semaine qui contient la date donnée.

**Exemples**

|Entrée                                    |Output|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()`est une variante obsolète de cette fonction. `weekofyear()`n’était pas conforme à l’ISO 8601; la première semaine d’une année a été définie comme la semaine avec le premier mercredi de l’année en elle.
La version actuelle de `week_of_year()`cette fonction, , est conforme ISO 8601; la première semaine d’une année est définie comme la semaine avec le premier jeudi de l’année en elle.