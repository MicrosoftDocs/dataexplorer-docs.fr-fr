---
title: endofweek() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit endofweek() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 83c080c60e34dbfdf19f7dde870621e34ded836d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515825"
---
# <a name="endofweek"></a>endofweek()

Retourne la fin de la semaine contenant la date, décalée par un décalage, si elle est fournie.

Le dernier jour de la semaine est considéré comme un samedi.

**Syntaxe**

`endofweek(`*date* `,`[*offset*]`)`

**Arguments**

* `date`: La date d’entrée.
* `offset`: Nombre facultatif de semaines offset à partir de la date d’entrée (integer, défaut - 0).

**Retourne**

Une date qui représente la fin de la semaine pour la valeur *de la date* donnée, avec la compensation, si spécifié.

**Exemple**

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|Week-end|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-07 23:59:59.9999999|
|2017-01-14 23:59:59.9999999|