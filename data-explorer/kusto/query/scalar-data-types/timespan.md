---
title: Le type de données de timespan - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le type de données de timespan dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31a0bfafed817ffaf531cffdcb844da8a357531f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509603"
---
# <a name="the-timespan-data-type"></a>Le type de données de timespan

Le `timespan` `time`type de données représente un intervalle de temps.

## <a name="timespan-literals"></a>littérals de timespan

Les littérals `timespan` de `timespan(`type ont la *valeur*`)`syntaxe , où un certain nombre de formats sont pris en charge pour la *valeur*, comme indiqué par le tableau suivant:

|||
---|---
`2d`|2 jours
`1.5h`|1,5 heure
`30m`|30 minutes
`10s`|10 secondes
`0.1s`|0,1 seconde
`100ms`| 100 millisecondes
`10microsecond`|10 microsecondes
`1tick`|100ns
`time(15 seconds)`|15 secondes
`time(2)`| 2 jours
`time(0.12:34:56.7)`|`0d+12h+34m+56.7s`

Le formulaire `time(null)` spécial est la [valeur nulle](null-values.md).

## <a name="timespan-operators"></a>opérateurs de timespan

Deux valeurs `timespan` de type peuvent être ajoutées, soustraites et divisées.
La dernière opération renvoie `real` une valeur de type représentant le nombre fractionnel de fois qu’une valeur peut s’adapter à l’autre.

## <a name="examples"></a>Exemples

L’exemple suivant calcule le nombre de secondes en une journée de plusieurs façons :

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```