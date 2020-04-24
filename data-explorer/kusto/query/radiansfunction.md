---
title: radians() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les radians () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0dc04c5f9593b6bd5fc61f57d20819cf7d2a178c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510657"
---
# <a name="radians"></a>radians()

Convertit la valeur de l’angle en degrés en valeur chez les radians, en utilisant la formule`radians = (PI / 180 ) * angle_in_degrees`

**Syntaxe**

`radians(`*Un*`)`

**Arguments**

* *a*: Angle en degrés (un nombre réel).

**Retourne**

* L’angle correspondant dans les radians pour un angle spécifié en degrés. 

**Exemples**

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1.5707963267949|3.14159265358979|6.28318530717959|