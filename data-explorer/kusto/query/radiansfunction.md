---
title: radians ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les radians () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bacb005b8828c63efac1737c527cc313a3ee052b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241322"
---
# <a name="radians"></a>radians()

Convertit la valeur d’angle en degrés en radians, à l’aide de la formule `radians = (PI / 180 ) * angle_in_degrees`

## <a name="syntax"></a>Syntaxe

`radians(`*un*`)`

## <a name="arguments"></a>Arguments

* *a*: angle en degrés (nombre réel).

## <a name="returns"></a>Retours

* Angle correspondant en radians pour un angle spécifié en degrés. 

## <a name="examples"></a>Exemples

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1,5707963267949|3,14159265358979|6.28318530717959|