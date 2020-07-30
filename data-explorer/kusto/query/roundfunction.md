---
title: Round ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Round () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c281d3347e82b429ded187ee142ea13fa7594567
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345740"
---
# <a name="round"></a>round()

Retourne la source arrondie à la précision spécifiée.

## <a name="syntax"></a>Syntaxe

`round(`*source* [ `,` *précision*]`)`

## <a name="arguments"></a>Arguments

* *source*: le scalaire source sur lequel l’arrondi est calculé.
* *Précision*: nombre de chiffres auxquels la source sera arrondie. (la valeur par défaut est 0)

## <a name="returns"></a>Retourne

Source arrondie à la précision spécifiée.

Round est différent de [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) , car le premier arrondit un nombre à un nombre spécifique de chiffres, tandis que le dernier arrondit la valeur à un entier multiple d’une taille d’emplacement donnée (Round (2.15, 1) retourne 2,2 alors que bin (2.15, 1) retourne 2).
 

## <a name="examples"></a>Exemples

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```