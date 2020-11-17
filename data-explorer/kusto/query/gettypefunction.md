---
title: GetType ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit GetType () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 84aa0d815328532f7a627958b56c41b998665d6b
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644601"
---
# <a name="gettype"></a>gettype()

Retourne le type au moment de l’exécution de son argument unique.

Le type au moment de l’exécution peut être différent du type nominal (statique) pour les expressions dont le type nominal est `dynamic` ; dans ce cas, `gettype()` il peut être utile de révéler le type de la valeur réelle (comment la valeur est encodée en mémoire).

## <a name="syntax"></a>Syntaxe

`gettype(`*Expr*`)`

## <a name="returns"></a>Retours

Chaîne représentant le type au moment de l’exécution de son argument unique.

## <a name="examples"></a>Exemples

|Expression                          |Retours      |
|------------------------------------|-------------|
|`gettype("a")`                      |`string`     |
|`gettype(111)`                      |`long`       |
|`gettype(1==1)`                     |`bool`       |
|`gettype(now())`                    |`datetime`   |
|`gettype(1s)`                       |`timespan`   |
|`gettype(parse_json('1'))`           |`int`        |
|`gettype(parse_json(' "abc" '))`     |`string`     |
|`gettype(parse_json(' {"abc":1} '))` |`dictionary` | 
|`gettype(parse_json(' [1, 2, 3] '))` |`array`      |
|`gettype(123.45)`                   |`real`       |
|`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))`|`guid`| 
|`gettype(parse_json(''))`            |`null`|
