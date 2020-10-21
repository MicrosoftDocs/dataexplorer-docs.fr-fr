---
title: endofweek ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit endofweek () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 47a5c9dd11af7282c38aabdd08b8b3bec571e57a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253030"
---
# <a name="endofweek"></a>endofweek()

Retourne la fin de la semaine contenant la date, décalée d’un décalage, si elle est fournie.

Le dernier jour de la semaine est considéré comme un samedi.

## <a name="syntax"></a>Syntaxe

`endofweek(`*Date* [ `,` *décalage*]`)`

## <a name="arguments"></a>Arguments

* `date`: Date d’entrée.
* `offset`: Nombre facultatif de semaines décalées à partir de la date d’entrée (entier, par défaut-0).

## <a name="returns"></a>Retours

Date/heure représentant la fin de la semaine pour la valeur de *Date* donnée, avec le décalage, s’il est spécifié.

## <a name="example"></a>Exemple

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|End|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-07 23:59:59.9999999|
|2017-01-14 23:59:59.9999999|