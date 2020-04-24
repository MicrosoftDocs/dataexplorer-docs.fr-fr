---
title: en tant qu’opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit comme opérateur dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05dc96fb7eec773d1e55d8b94a33cdda928622ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518426"
---
# <a name="as-operator"></a>opérateur as

Lie un nom à l’expression tabulaire d’entrée de l’opérateur, permettant ainsi à la requête de référencer la valeur de l’expression tabulaire plusieurs fois sans casser la requête et lier un nom par [l’instruction de laisser](letstatement.md).

**Syntaxe**

*T* `|` T `as` `hint.materialized` [ `=` ] *Nom* `true`

**Arguments**

* *T*: Une expression tabulaire.
* *Nom*: Un nom temporaire pour l’expression tabulaire.
* `hint.materialized`: Si `true`elle est réglée, la valeur de l’expression tabulaire sera matérialisée comme si elle était enveloppée par un appel de fonction [matérialisé..](./materializefunction.md)

**Remarques**

* Le nom `as` donné par sera `withsource=` utilisé dans `source_` la colonne de [l’union](./unionoperator.md), la colonne de [trouver](./findoperator.md), et la `$table` colonne de [recherche](./searchoperator.md).

* L’expression tabulaire nommée à l’aide de l’opérateur dans l’entrée tabulaire externe d’un [jointure](./joinoperator.md)(`$left`) peut également être utilisée dans l’entrée interne tabulaire de la jointure ().`$right`

**Exemples**

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