---
title: Substring ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Substring () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0e83e8d0baf33e5c11cb8b7ecafa607a08fe32b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350857"
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

**Remarques**

*startingIndex* peut être un nombre négatif, auquel cas la sous-chaîne sera récupérée à partir de la fin de la chaîne source.

## <a name="returns"></a>Retourne

Une sous-chaîne de la chaîne donnée. La sous-chaîne commence à la position de caractère startingIndex (de base zéro) et continue jusqu’à la fin de la chaîne ou des caractères de longueur si elle est spécifiée.

## <a name="examples"></a>Exemples

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```