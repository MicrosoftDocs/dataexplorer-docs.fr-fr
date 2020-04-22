---
title: array_iif() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit array_iif() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: f99b9aa8a9d081a7f28cd2e5bb8750b15f2fcdac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663916"
---
# <a name="array_iif"></a>array_iif()

Fonction iif élément-sage sur les tableaux dynamiques.

Un autre pseudonyme: array_iff().

**Syntaxe**

`array_iif(`*ConditionArray*, *IfTrue*, *IfFalse*]`)`

**Arguments**

* *conditionArray*: La gamme d’entrées de valeurs *boolean* ou numériques, doit être un tableau dynamique.
* *ifTrue*: Tableau d’entrée de valeurs ou valeur primitive - la valeur de résultat (s) lorsque la valeur correspondante de *ConditionArray* est *vraie.*
* *ifFalse*: Tableau d’entrée de valeurs ou valeur primitive - la valeur de résultat (s) lorsque la valeur correspondante de *ConditionArray* est *fausse*.

**Remarques**

* La longueur de résultat est la longueur de *conditionArray*.
* La valeur de l’état numérique est traitée comme *condition* ! *0*.
* La valeur de condition non numérique/nulle sera nulle dans l’indice correspondant du résultat.
* Les valeurs manquantes (dans des tableaux de longueur plus courtes) sont traitées comme nulles.

**Retourne**

Gamme dynamique des valeurs prises soit à partir des valeurs *IfTrue* ou *IfFalse* [array], selon la valeur correspondante du tableau de condition.

**Exemple**

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition|l|r|res|
|---|---|---|---|
|[vrai, faux, vrai]|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
