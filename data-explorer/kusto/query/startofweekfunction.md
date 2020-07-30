---
title: startOfWeek ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit startOfWeek () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 24763297585a7f043847e3037103a61650f65bd1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87343479"
---
# <a name="startofweek"></a>startofweek()

Retourne le début de la semaine contenant la date, décalée d’un décalage, si elle est fournie.

Le début de la semaine est considéré comme un dimanche.

## <a name="syntax"></a>Syntaxe

`startofweek(`*Date* [ `,` *décalage*]`)`

## <a name="arguments"></a>Arguments

* `date`: Date d’entrée.
* `offset`: Nombre facultatif de semaines décalées à partir de la date d’entrée (entier, par défaut-0).

## <a name="returns"></a>Retourne

DateTime représentant le début de la semaine pour la valeur de *Date* donnée, avec le décalage, s’il est spécifié.

## <a name="example"></a>Exemple

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|weekStart|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|