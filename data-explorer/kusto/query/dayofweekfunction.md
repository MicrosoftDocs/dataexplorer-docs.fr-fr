---
title: dayofweek() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit dayofweek() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31d3f525653f6e0979229e4355cdec6cb76833f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516301"
---
# <a name="dayofweek"></a>dayofweek()

Retourne le nombre de jours integer depuis `timespan`le dimanche précédent, comme un .

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
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```