---
title: sous-cordes() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le sous-cordon () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0273b3e93c8778af9c380f164faec74349aa8cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506696"
---
# <a name="substring"></a>substring()

Extrait un sous-cordon d’une chaîne source à partir d’un indice à la fin de la chaîne.

Éventuellement, la longueur de la sous-chaîne demandée peut être spécifiée.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

**Syntaxe**

`substring(`*source* `,` *startingIndex* [`,` *longueur*]`)`

**Arguments**

* *source*: La chaîne source dont le sous-cordon sera tiré.
* *startingIndex*: La position de caractère de départ à base zéro du sous-corde demandé.
* *longueur*: Un paramètre facultatif qui peut être utilisé pour spécifier le nombre demandé de caractères dans le sous-corde. 

**Remarques**

*startingIndex* peut être un nombre négatif, auquel cas le sous-corde sera récupéré à partir de l’extrémité de la chaîne source.

**Retourne**

Une sous-chaîne de la chaîne donnée. La sous-chaîne commence à la position de caractère startingIndex (de base zéro) et continue jusqu’à la fin de la chaîne ou des caractères de longueur si elle est spécifiée.

**Exemples**

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```