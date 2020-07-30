---
title: degrés ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les degrés () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41d679ea1add3706de5012f4e4fbf382e1f7b3ee
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348375"
---
# <a name="degrees"></a>degrees()

Convertit la valeur d’angle en radians en valeurs en degrés, à l’aide de la formule`degrees = (180 / PI ) * angle_in_radians`

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
