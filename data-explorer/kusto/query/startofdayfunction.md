---
title: startofday ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit startofday () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3b44da313c50360c73f63f1244ce2be959af2eb4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350942"
---
# <a name="startofday"></a>startofday()

Retourne le début de la journée contenant la date, décalée d’un décalage, si elle est fournie.

## <a name="syntax"></a>Syntaxe

`startofday(`*Date* [ `,` *décalage*]`)`

## <a name="arguments"></a>Arguments

* `date`: Date d’entrée.
* `offset`: Nombre facultatif de jours de décalage à partir de la date d’entrée (entier, par défaut-0). 

## <a name="returns"></a>Retourne

Valeur DateTime représentant le début de la journée pour la valeur de *Date* donnée, avec le décalage, s’il est spécifié.

## <a name="example"></a>Exemple

```kusto
  range offset from -1 to 1 step 1
 | project dayStart = startofday(datetime(2017-01-01 10:10:17), offset) 
```

|dayStart|
|---|
|2016-12-31 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-02 00:00:00.0000000|