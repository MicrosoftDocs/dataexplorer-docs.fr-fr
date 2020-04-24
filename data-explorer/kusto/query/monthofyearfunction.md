---
title: monthofyear() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit monthofyear() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ada34d3e6f6c905a1acfb550b02af3dab550fb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512340"
---
# <a name="monthofyear"></a>monthofyear()

Retourne le nombre d’intégraires représente le nombre de mois de l’année donnée.

Un autre alias: getmonth ()

```kusto
monthofyear(datetime("2015-12-14"))
```

**Syntaxe**

`monthofyear(`*a_date*`)`

**Arguments**

* `a_date` : une `datetime`.

**Retourne**

`month number`de l’année donnée.