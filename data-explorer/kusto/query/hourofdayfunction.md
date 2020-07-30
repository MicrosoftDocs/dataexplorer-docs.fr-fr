---
title: HourOfDay ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit HourOfDay () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a001a2f9faa1eb7ea3636ee6fbfde3cb0489158
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347508"
---
# <a name="hourofday"></a>hourofday()

Retourne le nombre entier représentant le numéro d’heure de la date donnée.

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

## <a name="syntax"></a>Syntaxe

`hourofday(`*a_date*`)`

## <a name="arguments"></a>Arguments

* `a_date` : une `datetime`.

## <a name="returns"></a>Retours

`hour number`du jour (0-23).