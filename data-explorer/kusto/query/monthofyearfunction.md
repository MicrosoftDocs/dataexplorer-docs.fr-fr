---
title: MonthOfYear ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit MonthOfYear () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: af015146bb2f07d83d4333312a96d5b80c67190a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346726"
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

## <a name="returns"></a>Retourne

`month number`de l’année donnée.