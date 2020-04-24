---
title: ronde () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit autour () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05111b6261c14f21d8d08e88a4c070faab32dc4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510232"
---
# <a name="round"></a>round()

Renvoie la source arrondie à la précision spécifiée.

**Syntaxe**

`round(`*source* `,` [ *Précision*]`)`

**Arguments**

* *source*: La source scalaire le tour est calculée.
* *Précision*: Nombre de chiffres à qui la source sera arrondie. (la valeur par défaut est de 0)

**Retourne**

La source arrondie à la précision spécifiée.

Le tour [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) est différent de ce que les premiers tours un nombre à un nombre spécifique de chiffres tandis que la valeur des derniers tours à un multiple d’un intégré de taille de bac (rond (rond (2.15, 1) retourne 2.2 tandis que bin (2.15, 1) retourne 2).
 

**Exemples**

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```