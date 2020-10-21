---
title: Arithmétiques DateTime/TimeSpan-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’arithmétique DateTime/TimeSpan dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 0b70cb1730d893e07790ade3079be21da209648c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247678"
---
# <a name="datetime--timespan-arithmetic"></a>Arithmétiques DateTime/TimeSpan

Kusto prend en charge l’exécution d’opérations arithmétiques sur des valeurs de types `datetime` et `timespan` :

* Il est possible de soustraire (mais pas d’ajouter) deux `datetime` valeurs pour obtenir une `timespan` valeur exprimant leur différence.
  Par exemple, `datetime(1997-06-25) - datetime(1910-06-11)` est l’ancien [Jacques-Yves Cousteau](https://en.wikipedia.org/wiki/Jacques_Cousteau) lorsqu’il est mort.

* Il est possible d’ajouter ou de soustraire deux `timespan` valeurs pour obtenir une `timespan` valeur qui est sa somme ou sa différence.
  Par exemple, `1d + 2d` est de trois jours.

* Il est possible d’ajouter ou de soustraire une `timespan` valeur d’une `datetime` valeur.
  Par exemple, `datetime(1910-06-11) + 1d` est la date à laquelle Cousteau a été réactivée un jour.

* Il est possible de diviser deux `timespan` valeurs pour qu’elles obtiennent leur quotient.
  Par exemple, `1d / 5h` donne `4.8` .
  Cela donne à l’une la possibilité d’exprimer toute `timespan` valeur en tant que multiple d’une autre `timespan` valeur. Par exemple, pour exprimer une heure en secondes, il suffit de diviser `1h` par `1s` : `1h / 1s` (avec le résultat évident, `3600` ).

* À l’inverse, une valeur peut avoir plusieurs valeurs numériques (telles que `double` et `long` ) par `timespan` valeur pour obtenir une `timespan` valeur.
  Par exemple, vous pouvez exprimer une heure et une moitié sous la forme `1.5 * 1h` .

## <a name="example-unix-time"></a>Exemple : heure UNIX

L' [heure UNIX](https://en.wikipedia.org/wiki/Unix_time) (également connue sous le nom de temps POSIX ou d’époque UNIX) est un système permettant de décrire un point dans le temps en fonction du nombre de secondes qui se sont écoulées depuis le jeudi 00:00:00, du 1er janvier 1970, du temps universel coordonné (UTC), moins les secondes bissextiles.

Si vos données incluent une représentation de l’heure UNIX sous la forme d’un entier, ou si vous avez besoin de les convertir, les fonctions suivantes sont disponibles :

```kusto
let fromUnixTime = (t:long)
{ 
    datetime(1970-01-01) + t * 1sec 
};
print result = fromUnixTime(1546897531)
```

|result                     |
|---------------------------|
|2019-01-07 21:45:31.0000000|

```kusto
let toUnixTime = (dt:datetime) 
{ 
    (dt - datetime(1970-01-01)) / 1s 
};
print result = toUnixTime(datetime(2019-01-07 21:45:31.0000000))
```

|result                     |
|---------------------------|
|1546897531                 |

> [!NOTE]
> Outre les fonctions ci-dessus, consultez Fonctions dédiées pour les conversions d’heure d’époque UNIX : [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md) 
>  [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md) 
>  [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md) 
>  [unixtime_nanoseconds_todatetime (](unixtime-nanoseconds-todatetimefunction.md) )