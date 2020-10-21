---
title: STARTOFYEAR ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit STARTOFYEAR () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 973ea6d5043db0f173fa0ebe98548b20968371b3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241171"
---
# <a name="startofyear"></a>startofyear()

Retourne le début de l’année contenant la date, décalée d’un décalage, si elle est fournie.

## <a name="syntax"></a>Syntaxe

`startofyear(`*Date* [ `,` *décalage*]`)`

## <a name="arguments"></a>Arguments

* `date`: Date d’entrée.
* `offset`: Nombre facultatif d’années de décalage à partir de la date d’entrée (entier, par défaut-0). 

## <a name="returns"></a>Retours

Valeur DateTime représentant le début de l’année pour la valeur de *Date* donnée, avec le décalage, s’il est spécifié.

## <a name="example"></a>Exemple

```kusto
  range offset from -1 to 1 step 1
 | project yearStart = startofyear(datetime(2017-01-01 10:10:17), offset) 
```

|yearStart|
|---|
|2016-01-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2018-01-01 00:00:00.0000000|