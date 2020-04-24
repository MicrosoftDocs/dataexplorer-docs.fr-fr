---
title: startofday() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit startofday() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6517b50ca880085761212ae9037cee96d20a3269
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507274"
---
# <a name="startofday"></a>startofday()

Retourne le début de la journée contenant la date, décalé par un décalage, si fourni.

**Syntaxe**

`startofday(`*date* `,`[*offset*]`)`

**Arguments**

* `date`: La date d’entrée.
* `offset`: Nombre facultatif de jours de compensation à partir de la date d’entrée (integer, défaut - 0). 

**Retourne**

Une date qui représente le début de la journée pour la valeur *de la date* donnée, avec la compensation, si spécifié.

**Exemple**

```kusto
  range offset from -1 to 1 step 1
 | project dayStart = startofday(datetime(2017-01-01 10:10:17), offset) 
```

|dayStart|
|---|
|2016-12-31 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-02 00:00:00.0000000|