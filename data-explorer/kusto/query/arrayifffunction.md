---
title: array_iif ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit array_iif () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: 7f11e0c00b19fd24ed2db326f3209236ecf25ff6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246898"
---
# <a name="array_iif"></a>array_iif()

Fonction IIF au niveau des éléments sur les tableaux dynamiques.

Autre alias : array_iff ().

## <a name="syntax"></a>Syntaxe

`array_iif(`*ConditionArray*, *IfTrue*, *IfFalse*]`)`

## <a name="arguments"></a>Arguments

* *conditionArray*: tableau d’entrée de valeurs *booléennes* ou numériques, qui doit être un tableau dynamique.
* *IfTrue*: tableau d’entrée de valeurs ou valeur primitive-la ou les valeurs de résultat lorsque la valeur correspondante de *ConditionArray* est *true*.
* *IfFalse*: tableau d’entrée de valeurs ou valeur primitive-valeur (s) de résultat lorsque la valeur correspondante de *ConditionArray* est *false*.

**Notes**

* La longueur du résultat est la longueur de *conditionArray*.
* La valeur de condition numérique est traitée comme *condition* ! = *0*.
* La valeur de condition non numérique/null aura null dans l’index correspondant du résultat.
* Les valeurs manquantes (dans des tableaux de longueur plus courts) sont traitées comme null.

## <a name="returns"></a>Retours

Tableau dynamique des valeurs prises à partir des valeurs *IfTrue* ou *IfFalse* [array], en fonction de la valeur correspondante du tableau de conditions.

## <a name="example"></a>Exemple

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition|l|r|res|
|---|---|---|---|
|[true, false, true]|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
