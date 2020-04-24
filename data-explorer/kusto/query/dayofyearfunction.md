---
title: dayofyear() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit dayofyear() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41e7c5906da001e877dd9124124e126d729e886d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516216"
---
# <a name="dayofyear"></a>dayofyear()

Retourne le nombre d’intégraires représente le nombre de jours de l’année donnée.

```kusto
dayofyear(datetime(2015-12-14))
```

**Syntaxe**

`dayofweek(`*a_date*`)`

**Arguments**

* `a_date` : une `datetime`.

**Retourne**

`day number`de l’année donnée.