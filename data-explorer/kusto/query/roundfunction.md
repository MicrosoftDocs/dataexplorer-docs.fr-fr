---
title: Round ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Round () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90d424929fe0b2034e4778ca2167e1e14dfbf79e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242902"
---
# <a name="round"></a>round()

Retourne la source arrondie à la précision spécifiée.

## <a name="syntax"></a>Syntaxe

`round(`*source* [ `,` *précision*]`)`

## <a name="arguments"></a>Arguments

* *source*: le scalaire source sur lequel l’arrondi est calculé.
* *Précision*: nombre de chiffres auxquels la source sera arrondie. (la valeur par défaut est 0)

## <a name="returns"></a>Retours

Source arrondie à la précision spécifiée.

Round est différent de [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) , car le premier arrondit un nombre à un nombre spécifique de chiffres, tandis que le dernier arrondit la valeur à un entier multiple d’une taille d’emplacement donnée (Round (2.15, 1) retourne 2,2 alors que bin (2.15, 1) retourne 2).
 

## <a name="examples"></a>Exemples

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```