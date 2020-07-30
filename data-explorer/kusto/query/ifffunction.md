---
title: IFF ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit IFF () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7eeab87f3c3ef42d1e00bf0d6b8853fe3a2f3125
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347491"
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