---
title: startOfWeek ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit startOfWeek () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5f53dfca4183c4dae623b41abf4c5bce6a3e828
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241180"
---
# <a name="startofweek"></a>startofweek()

Retourne le début de la semaine contenant la date, décalée d’un décalage, si elle est fournie.

Le début de la semaine est considéré comme un dimanche.

## <a name="syntax"></a>Syntaxe

`startofweek(`*Date* [ `,` *décalage*]`)`

## <a name="arguments"></a>Arguments

* `date`: Date d’entrée.
* `offset`: Nombre facultatif de semaines décalées à partir de la date d’entrée (entier, par défaut-0).

## <a name="returns"></a>Retours

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