---
title: StartOfMonth ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit StartOfMonth () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7e13cf809174fd258f9e7245bb2e537e2408c28
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241749"
---
# <a name="startofmonth"></a>startofmonth()

Retourne le début du mois contenant la date, décalée par un décalage, s’il est fourni.

## <a name="syntax"></a>Syntaxe

`startofmonth(`*Date* [ `,` *décalage*]`)`

## <a name="arguments"></a>Arguments

* `date`: Date d’entrée.
* `offset`: Nombre facultatif de mois de décalage à partir de la date d’entrée (entier, par défaut-0).

## <a name="returns"></a>Retours

Valeur DateTime représentant le début du mois pour la valeur de *Date* donnée, avec le décalage, s’il est spécifié.

## <a name="example"></a>Exemple

```kusto
  range offset from -1 to 1 step 1
 | project monthStart = startofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|monthStart|
|---|
|2016-12-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-02-01 00:00:00.0000000|