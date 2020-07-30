---
title: StartOfMonth ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit StartOfMonth () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e965e0ae8b3783b396cfc4ea5e0ecf1e34b818a9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87343836"
---
# <a name="startofmonth"></a>startofmonth()

Retourne le début du mois contenant la date, décalée par un décalage, s’il est fourni.

## <a name="syntax"></a>Syntaxe

`startofmonth(`*Date* [ `,` *décalage*]`)`

## <a name="arguments"></a>Arguments

* `date`: Date d’entrée.
* `offset`: Nombre facultatif de mois de décalage à partir de la date d’entrée (entier, par défaut-0).

## <a name="returns"></a>Retourne

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