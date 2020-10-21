---
title: week_of_year ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit week_of_year () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 18cad42dfd0f652daa4c8da80524ba40ace9b153
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251236"
---
# <a name="week_of_year"></a>week_of_year ()

Retourne un entier qui représente le numéro de la semaine. Le numéro de semaine est calculé à partir de la première semaine de l’année, qui est celle qui comprend le premier jeudi, conformément à la [norme ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).

```kusto
week_of_year(datetime("2015-12-14"))
```

## <a name="syntax"></a>Syntaxe

`week_of_year(`*a_date*`)`

## <a name="arguments"></a>Arguments

* `a_date` : une `datetime`.

## <a name="returns"></a>Retours

`week number` : Numéro de semaine qui contient la date donnée.

## <a name="examples"></a>Exemples

|Entrée                                    |Output|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()` est une variante obsolète de cette fonction. `weekofyear()` n’était pas conforme à la norme ISO 8601 ; la première semaine de l’année a été définie en tant que semaine avec le premier mercredi de l’année.
La version actuelle de cette fonction, `week_of_year()` , est conforme à la norme ISO 8601 ; la première semaine de l’année est définie comme la semaine avec le premier jeudi de l’année.