---
title: IIF ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit IIF () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0d912f94a224b073fe9214f70077067d3a24c906
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347474"
---
# <a name="iif"></a>iif()

Évalue le premier argument (le prédicat) et retourne la valeur du deuxième ou du troisième argument, selon que le prédicat est évalué à `true` (deuxième) ou `false` (troisième).

Les deuxième et troisième arguments doivent être de même type.

## <a name="syntax"></a>Syntaxe

`iif(`*prédicat* `,` *IfTrue* `,` *IfFalse*`)`

## <a name="arguments"></a>Arguments

* *predicate*: expression qui prend une `boolean` valeur.
* *IfTrue*: expression qui est évaluée et sa valeur retournée par la fonction si le *prédicat* prend la valeur `true` .
* *IfFalse*: expression qui est évaluée et sa valeur retournée par la fonction si le *prédicat* prend la valeur `false` .

## <a name="returns"></a>Retours

Cette fonction retourne la valeur de *ifTrue* si *predicate* a la valeur `true`,ou la valeur de *ifFalse* dans le cas contraire.

## <a name="example"></a>Exemple

```kusto
T 
| extend day = iif(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

Alias pour [`iff()`](ifffunction.md) .