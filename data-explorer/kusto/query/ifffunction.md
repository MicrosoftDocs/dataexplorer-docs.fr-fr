---
title: IFF ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit IFF () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5cc6a41c8b74e4fd08eebbe968b7384dce39039e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252283"
---
# <a name="iff"></a>iff()

Évalue le premier argument (le prédicat) et retourne la valeur du deuxième ou du troisième argument, selon que le prédicat est évalué à `true` (deuxième) ou `false` (troisième).

Les deuxième et troisième arguments doivent être de même type.

## <a name="syntax"></a>Syntaxe

`iff(`*prédicat* `,` *IfTrue* `,` *IfFalse*`)`

## <a name="arguments"></a>Arguments

* *predicate*: expression qui prend une `boolean` valeur.
* *IfTrue*: expression qui est évaluée et sa valeur retournée par la fonction si le *prédicat* prend la valeur `true` .
* *IfFalse*: expression qui est évaluée et sa valeur retournée par la fonction si le *prédicat* prend la valeur `false` .

## <a name="returns"></a>Retours

Cette fonction retourne la valeur de *ifTrue* si *predicate* a la valeur `true`,ou la valeur de *ifFalse* dans le cas contraire.

## <a name="example"></a>Exemple

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```