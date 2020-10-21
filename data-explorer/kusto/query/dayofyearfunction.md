---
title: DayOfYear ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit DayOfYear () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2ac301365cb22849dc07ea137756f4093bea79b2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252383"
---
# <a name="dayofyear"></a>dayofyear()

Retourne le nombre entier représente le nombre de jours de l’année donnée.

```kusto
dayofyear(datetime(2015-12-14))
```

## <a name="syntax"></a>Syntaxe

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>Arguments

* `a_date` : une `datetime`.

## <a name="returns"></a>Retours

`day number` de l’année donnée.