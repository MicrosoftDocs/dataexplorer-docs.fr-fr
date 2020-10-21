---
title: endofyear ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit endofyear () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a93b24af949beedc02ddd3dbbc479074f0aa891c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249137"
---
# <a name="endofyear"></a>endofyear()

Retourne la fin de l’année contenant la date, décalée d’un décalage, si elle est fournie.

## <a name="syntax"></a>Syntaxe

`endofyear(`*Date* [ `,` *décalage*]`)`

## <a name="arguments"></a>Arguments

* `date`: Date d’entrée.
* `offset`: Nombre facultatif d’années de décalage à partir de la date d’entrée (entier, par défaut-0).

## <a name="returns"></a>Retours

Date/heure représentant la fin de l’année pour la valeur de *Date* donnée, avec le décalage, s’il est spécifié.

## <a name="example"></a>Exemple

```kusto
  range offset from -1 to 1 step 1
 | project yearEnd = endofyear(datetime(2017-01-01 10:10:17), offset) 
```

|yearEnd|
|---|
|2016-12-31 23:59:59.9999999|
|2017-12-31 23:59:59.9999999|
|2018-12-31 23:59:59.9999999|