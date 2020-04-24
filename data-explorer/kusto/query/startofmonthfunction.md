---
title: startofmonth() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit startofmonth() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e220919f6b09dbcd2519c67f48148a6375395e7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507240"
---
# <a name="startofmonth"></a>startofmonth()

Retourne le début du mois contenant la date, décalé par un décalage, si fourni.

**Syntaxe**

`startofmonth(`*date* `,`[*offset*]`)`

**Arguments**

* `date`: La date d’entrée.
* `offset`: Nombre facultatif de mois de compensation à partir de la date d’entrée (integer, défaut - 0).

**Retourne**

Une date qui représente le début du mois pour la valeur *de la date* donnée, avec la compensation, si spécifié.

**Exemple**

```kusto
  range offset from -1 to 1 step 1
 | project monthStart = startofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|monthStart|
|---|
|2016-12-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-02-01 00:00:00.0000000|