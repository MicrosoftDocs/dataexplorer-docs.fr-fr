---
title: endofday ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit endofday () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d87f188cf0ae3639df4261d1813ebbe2a6788192
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245082"
---
# <a name="endofday"></a>endofday()

Retourne la fin de la journée contenant la date, décalée d’un décalage, si elle est fournie.

## <a name="syntax"></a>Syntaxe

`endofday(`*Date* [ `,` *décalage*]`)`

## <a name="arguments"></a>Arguments

* `date`: Date d’entrée.
* `offset`: Nombre facultatif de jours de décalage à partir de la date d’entrée (entier, par défaut-0).

## <a name="returns"></a>Retours

Date/heure représentant la fin de la journée pour la valeur de *Date* donnée, avec le décalage, s’il est spécifié.

## <a name="example"></a>Exemple

```kusto
  range offset from -1 to 1 step 1
 | project dayEnd = endofday(datetime(2017-01-01 10:10:17), offset) 
```

|dayEnd|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-01 23:59:59.9999999|
|2017-01-02 23:59:59.9999999|