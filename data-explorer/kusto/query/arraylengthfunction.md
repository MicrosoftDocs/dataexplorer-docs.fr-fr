---
title: array_length ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit array_length () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14203e3078b7fe30222ea26320ed1391000d5c05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349548"
---
# <a name="array_length"></a>array_length()

Calcule le nombre d’éléments dans un tableau dynamique.

## <a name="syntax"></a>Syntaxe

`array_length(`*array*`)`

## <a name="arguments"></a>Arguments

* *tableau*: `dynamic` valeur.

## <a name="returns"></a>Retourne

Nombre d’éléments dans *array*, ou `null` si *array* n’est pas un tableau.

## <a name="examples"></a>Exemples

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```