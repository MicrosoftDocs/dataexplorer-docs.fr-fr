---
title: endofyear() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit endofyear() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4a14d1cc42d5b9116e8a144e2b67fb74c1932ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515774"
---
# <a name="endofyear"></a>endofyear()

Rendements de la fin de l’année contenant la date, décalé par compensation, si fourni.

**Syntaxe**

`endofyear(`*date* `,`[*offset*]`)`

**Arguments**

* `date`: La date d’entrée.
* `offset`: Un nombre facultatif d’années de compensation à partir de la date d’entrée (integer, défaut - 0).

**Retourne**

Une date qui représente la fin de l’année pour la valeur *de la date* donnée, avec la compensation, si spécifié.

**Exemple**

```kusto
  range offset from -1 to 1 step 1
 | project yearEnd = endofyear(datetime(2017-01-01 10:10:17), offset) 
```

|yearEnd|
|---|
|2016-12-31 23:59:59.9999999|
|2017-12-31 23:59:59.9999999|
|2018-12-31 23:59:59.9999999|