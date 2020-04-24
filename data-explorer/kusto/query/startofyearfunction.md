---
title: startofyear() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit startofyear() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f91c749cc3833954d902eb4ebd7e230e32e3a991
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507206"
---
# <a name="startofyear"></a>startofyear()

Retourne le début de l’année contenant la date, décalé par un décalage, s’il est fourni.

**Syntaxe**

`startofyear(`*date* `,`[*offset*]`)`

**Arguments**

* `date`: La date d’entrée.
* `offset`: Un nombre facultatif d’années de compensation à partir de la date d’entrée (integer, défaut - 0). 

**Retourne**

Une date qui représente le début de l’année pour la valeur *de la date* donnée, avec la compensation, si spécifié.

**Exemple**

```kusto
  range offset from -1 to 1 step 1
 | project yearStart = startofyear(datetime(2017-01-01 10:10:17), offset) 
```

|annéeStart|
|---|
|2016-01-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2018-01-01 00:00:00.0000000|