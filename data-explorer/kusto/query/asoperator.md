---
title: opérateur as-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur as dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 15dcf79938f4b83f18055b6f59a9b70998ab6049
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252817"
---
# <a name="as-operator"></a>opérateur as

Lie un nom à l’expression tabulaire d’entrée de l’opérateur, ce qui permet à la requête de référencer la valeur de l’expression tabulaire plusieurs fois sans rompre la requête et lier un nom à l’aide de l' [instruction Let](letstatement.md).

## <a name="syntax"></a>Syntaxe

*T* `|` `as` [ `hint.materialized` `=` `true` ] *nom*

## <a name="arguments"></a>Arguments

* *T*: expression tabulaire.
* *Nom*: nom temporaire de l’expression tabulaire.
* `hint.materialized`: Si `true` la valeur est, la valeur de l’expression tabulaire est matérialisée comme si elle était encapsulée par un appel de fonction [matérial ()](./materializefunction.md) .

> [!NOTE]
> * Le nom donné par `as` sera utilisé dans la `withsource=` colonne de [Union](./unionoperator.md), la `source_` colonne de recherche [find](./findoperator.md)et la `$table` colonne de [recherche](./searchoperator.md).
> * L’expression tabulaire nommée à l’aide de l’opérateur dans l’entrée tabulaire externe d’une [jointure](./joinoperator.md)( `$left` ) peut également être utilisée dans l’entrée interne tabulaire de la jointure ( `$right` ).

## <a name="examples"></a>Exemples

```kusto
// 1. In the following 2 example the union's generated TableName column will consist of 'T1' and 'T2'
range x from 1 to 10 step 1 
| as T1 
| union withsource=TableName T2

union withsource=TableName (range x from 1 to 10 step 1 | as T1), T2

// 2. In the following example, the 'left side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Start"
//    and the 'right side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Stop"
MyLogTable  
| where type == "Event"
| as T
| where Name == "Start"
| join (
    T
    | where Name == "Stop"
) on ActivityId
```