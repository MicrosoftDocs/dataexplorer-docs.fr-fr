---
title: endofday() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit endofday() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a25f6b9c4ba660fbb8a50390d9d6920ff21caeb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515876"
---
# <a name="endofday"></a>endofday()

Retourne la fin de la journée contenant la date, décalée par un décalage, si elle est fournie.

**Syntaxe**

`endofday(`*date* `,`[*offset*]`)`

**Arguments**

* `date`: La date d’entrée.
* `offset`: Nombre facultatif de jours de compensation à partir de la date d’entrée (integer, défaut - 0).

**Retourne**

Une date qui représente la fin de la journée pour la valeur *de la date* donnée, avec la compensation, si spécifié.

**Exemple**

```kusto
  range offset from -1 to 1 step 1
 | project dayEnd = endofday(datetime(2017-01-01 10:10:17), offset) 
```

|dayEnd|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-01 23:59:59.9999999|
|2017-01-02 23:59:59.9999999|