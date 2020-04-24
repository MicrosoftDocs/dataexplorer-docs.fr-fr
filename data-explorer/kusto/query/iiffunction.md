---
title: iif() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit iif() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36ba98b9677055dffce32911d80e67a9161b673b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514040"
---
# <a name="iif"></a>iif()

Évalue le premier argument (le prédicat) et renvoie la valeur du deuxième ou du `true` troisième argument, `false` selon que le prédicat évalué à (deuxième) ou (troisième).

Les deuxième et troisième arguments doivent être de même type.

**Syntaxe**

`iif(`*predicate* `,` *ifTrue* `,` *ifFalse*`)`

**Arguments**

* *prédicat*: Une expression qui `boolean` évalue à une valeur.
* *ifTrue*: Une expression qui est évaluée et sa valeur restituée de la fonction si *le prédicat* évalue à `true`.
* *ifFalse*: Une expression qui est évaluée et sa valeur restituée de la fonction si *le prédicat s’évalue* à `false`.

**Retourne**

Cette fonction retourne la valeur de *ifTrue* si *predicate* a la valeur `true`,ou la valeur de *ifFalse* dans le cas contraire.

**Exemple**

```kusto
T 
| extend day = iif(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

Un alias [`iff()`](ifffunction.md)pour .