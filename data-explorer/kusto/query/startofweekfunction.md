---
title: startofweek() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit startofweek() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9705b586a0b8c5161b7d4c27735f644b6982c707
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507223"
---
# <a name="startofweek"></a>startofweek()

Retourne le début de la semaine contenant la date, décalé par un décalage, si fourni.

Le début de la semaine est considéré comme un dimanche.

**Syntaxe**

`startofweek(`*date* `,`[*offset*]`)`

**Arguments**

* `date`: La date d’entrée.
* `offset`: Nombre facultatif de semaines offset à partir de la date d’entrée (integer, défaut - 0).

**Retourne**

Une date qui représente le début de la semaine pour la valeur *de la date* donnée, avec la compensation, si spécifié.

**Exemple**

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|semaineStart|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|