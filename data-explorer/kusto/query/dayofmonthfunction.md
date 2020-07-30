---
title: DayOfMonth ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit DayOfMonth () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3341f416642b06d899c2a3d1f6675f4d3254291f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348494"
---
# <a name="dayofmonth"></a>dayofmonth()

Retourne le nombre entier représentant le nombre de jours du mois donné.

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

## <a name="syntax"></a>Syntaxe

`dayofmonth(`*a_date*`)`

## <a name="arguments"></a>Arguments

* `a_date` : une `datetime`.

## <a name="returns"></a>Retours

`day number`du mois donné.