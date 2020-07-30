---
title: radians ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les radians () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3aaa41a631498e2938acf722b75f409a1bbe5031
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345944"
---
# <a name="radians"></a>radians()

Convertit la valeur d’angle en degrés en radians, à l’aide de la formule`radians = (PI / 180 ) * angle_in_degrees`

## <a name="syntax"></a>Syntaxe

`radians(`*un*`)`

## <a name="arguments"></a>Arguments

* *a*: angle en degrés (nombre réel).

## <a name="returns"></a>Retourne

* Angle correspondant en radians pour un angle spécifié en degrés. 

## <a name="examples"></a>Exemples

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1,5707963267949|3,14159265358979|6.28318530717959|