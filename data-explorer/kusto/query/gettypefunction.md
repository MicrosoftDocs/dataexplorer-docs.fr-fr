---
title: GetType ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit GetType () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 0efa07b7a1b050fe81ce2f369e8df5af4c05e212
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347661"
---
# <a name="gettype"></a>gettype()

Retourne le type au moment de l’exécution de son argument unique.

Le type au moment de l’exécution peut être différent du type nominal (statique) pour les expressions dont le type nominal est `dynamic` ; dans ce cas, `gettype()` il peut être utile de révéler le type l’de la valeur réelle (comment la valeur est encodée en mémoire).

## <a name="syntax"></a>Syntaxe

`gettype(`*Expr*`)`

## <a name="returns"></a>Retourne

Chaîne représentant le type au moment de l’exécution de son argument unique.

## <a name="examples"></a>Exemples

|Expression                          |Retourne      |
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