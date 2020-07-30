---
title: DayOfYear ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit DayOfYear () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 925b65846c6ba32163bd325fd2ee3321bc7fc802
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348460"
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

## <a name="returns"></a>Retourne

`day number`de l’année donnée.