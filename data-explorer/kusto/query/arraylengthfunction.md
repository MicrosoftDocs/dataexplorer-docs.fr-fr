---
title: array_length ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit array_length () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3777194270e91d75d68c3af544e18b62358f709e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246784"
---
# <a name="array_length"></a>array_length()

Calcule le nombre d’éléments dans un tableau dynamique.

## <a name="syntax"></a>Syntaxe

`array_length(`*array*`)`

## <a name="arguments"></a>Arguments

* *tableau*: `dynamic` valeur.

## <a name="returns"></a>Retours

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