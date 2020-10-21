---
title: Substring ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Substring () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3780aac9ad2675e901ffff63a89177b478d461ea
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251391"
---
# <a name="substring"></a>substring()

Extrait une sous-chaîne d’une chaîne source à partir d’un index jusqu’à la fin de la chaîne.

Éventuellement, la longueur de la sous-chaîne demandée peut être spécifiée.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>Syntaxe

`substring(`*source* `,` *startingIndex* [ `,` *longueur*]`)`

## <a name="arguments"></a>Arguments

* *source*: chaîne source à partir de laquelle la sous-chaîne sera extraite.
* *startingIndex*: position de caractère de départ de base zéro de la sous-chaîne demandée.
* *Length*: paramètre facultatif qui peut être utilisé pour spécifier le nombre de caractères demandé dans la sous-chaîne. 

**Notes**

*startingIndex* peut être un nombre négatif, auquel cas la sous-chaîne sera récupérée à partir de la fin de la chaîne source.

## <a name="returns"></a>Retours

Une sous-chaîne de la chaîne donnée. La sous-chaîne commence à la position de caractère startingIndex (de base zéro) et continue jusqu’à la fin de la chaîne ou des caractères de longueur si elle est spécifiée.

## <a name="examples"></a>Exemples

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```