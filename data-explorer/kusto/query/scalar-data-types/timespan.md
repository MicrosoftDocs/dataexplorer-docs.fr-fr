---
title: Type de données TimeSpan-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le type de données TimeSpan dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 204076e8ed079dec69cae7080e7d2c50df52a9a6
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610319"
---
# <a name="the-timespan-data-type"></a>Type de données TimeSpan

Le `timespan` `time` type de données () représente un intervalle de temps.

## <a name="timespan-literals"></a>littéraux TimeSpan

Les littéraux de type `timespan` ont la `timespan(` *valeur*de syntaxe `)` , où un certain nombre de formats sont pris en charge pour la *valeur*, comme indiqué dans le tableau suivant :

|Value|Durée|
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

La forme spéciale `time(null)` est la [valeur null](null-values.md).

## <a name="timespan-operators"></a>TimeSpan, opérateurs

Deux valeurs de type `timespan` peuvent être ajoutées, soustraites et divisées.
La dernière opération retourne une valeur de type `real` représentant le nombre fractionnaire de fois où une valeur peut correspondre à l’autre.

## <a name="examples"></a>Exemples

L’exemple suivant calcule le nombre de secondes dans un jour de plusieurs façons :

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```