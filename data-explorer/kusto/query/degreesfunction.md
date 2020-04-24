---
title: degrés() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les degrés () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1365d6c6629f4f4769d7c4b62491eaec25e4ec59
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516029"
---
# <a name="degrees"></a>degrees()

Convertit la valeur d’angle en radians en valeur en degrés, en utilisant la formule`degrees = (180 / PI ) * angle_in_radians`

**Syntaxe**

`degrees(`*Un*`)`

**Arguments**

* *a*: Angle in radians (un vrai nombre).

**Retourne**

* L’angle correspondant en degrés pour un angle spécifié dans les radians. 

**Exemples**

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|degrés0|degrés1|degrés2|
|---|---|---|
|45|270|0|
