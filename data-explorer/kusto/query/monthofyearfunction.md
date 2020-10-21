---
title: MonthOfYear ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit MonthOfYear () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd40612236b9c9b249c9c070bc784c518be0faae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250994"
---
# <a name="monthofyear"></a>monthofyear()

Retourne le nombre entier représentant le mois de l’année donnée.

Autre alias : GetMonth ()

```kusto
monthofyear(datetime("2015-12-14"))
```

## <a name="syntax"></a>Syntaxe

`monthofyear(`*a_date*`)`

## <a name="arguments"></a>Arguments

* `a_date` : une `datetime`.

## <a name="returns"></a>Retours

`month number` de l’année donnée.