---
title: endofmonth() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit endofmonth() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebab067c730e3cd61c84ae33eba4e49d7f0b0c0c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515859"
---
# <a name="endofmonth"></a>endofmonth()

Retourne la fin du mois contenant la date, décalée par un décalage, si elle est fournie.

**Syntaxe**

`endofmonth(`*date* `,`[*offset*]`)`

**Arguments**

* `date`: La date d’entrée.
* `offset`: Nombre facultatif de mois de compensation à partir de la date d’entrée (integer, défaut - 0).

**Retourne**

Une date qui représente la fin du mois pour la valeur *de la date* donnée, avec la compensation, si spécifié.

**Exemple**

```kusto
  range offset from -1 to 1 step 1
 | project monthEnd = endofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|monthEnd|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-31 23:59:59.9999999|
|2017-02-28 23:59:59.9999999|