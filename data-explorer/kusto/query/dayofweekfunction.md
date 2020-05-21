---
title: DayOfWeek ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit DayOfWeek () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e445a86b976f251de2beef4726c4840bcec8e44
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722029"
---
# <a name="dayofweek"></a>dayofweek()

Retourne le nombre entier de jours depuis le dimanche précédent, sous la forme d’un `timespan` .

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

**Syntaxe**

`dayofweek(`*a_date*`)`

**Arguments**

* `a_date` : une `datetime`.

**Retourne**

`timespan` depuis minuit au début du dimanche précédent, arrondi au nombre entier inférieur de jours.

**Exemples**

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
