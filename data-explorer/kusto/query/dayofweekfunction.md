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
ms.openlocfilehash: 04b6122c7517d79d5563892a621eed8cde3b948a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348477"
---
# <a name="dayofweek"></a>dayofweek()

Retourne le nombre entier de jours depuis le dimanche précédent, sous la forme d’un `timespan` .

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

## <a name="syntax"></a>Syntaxe

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>Arguments

* `a_date` : une `datetime`.

## <a name="returns"></a>Retours

`timespan` depuis minuit au début du dimanche précédent, arrondi au nombre entier inférieur de jours.

## <a name="examples"></a>Exemples

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
