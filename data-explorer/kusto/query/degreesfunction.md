---
title: degrés ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les degrés () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 04efbee59bce153de76ab5d44617b8d1bebc121c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253078"
---
# <a name="degrees"></a>degrees()

Convertit la valeur d’angle en radians en valeurs en degrés, à l’aide de la formule `degrees = (180 / PI ) * angle_in_radians`

## <a name="syntax"></a>Syntaxe

`degrees(`*un*`)`

## <a name="arguments"></a>Arguments

* *a*: angle en radians (nombre réel).

## <a name="returns"></a>Retours

* Angle correspondant en degrés pour un angle spécifié en radians. 

## <a name="examples"></a>Exemples

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|degrees0|degrees1|degrees2|
|---|---|---|
|45|270|0|
