---
title: Datetime / timepan arithmétique - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Datetime / timepan arithmétique dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 34bda7b8418dd519995f25c625a5031202a64a8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516318"
---
# <a name="datetime--timespan-arithmetic"></a>Datetime / timepan arithmétique

Kusto soutient l’exécution des opérations `datetime` d’arithmétique sur les valeurs des types et `timespan`:

* On peut soustraire (mais `datetime` pas ajouter) deux valeurs pour obtenir une `timespan` valeur exprimant leur différence.
  Par exemple, `datetime(1997-06-25) - datetime(1910-06-11)` c’est l’âge de [Jacques-Yves Cousteau](https://en.wikipedia.org/wiki/Jacques_Cousteau) à sa mort.

* On peut ajouter ou `timespan` soustraire `timespan` deux valeurs pour obtenir une valeur qui est leur somme ou leur différence.
  Par exemple, c’est `1d + 2d` trois jours.

* On peut ajouter ou `timespan` soustraire une valeur d’une `datetime` valeur.
  Par exemple, `datetime(1910-06-11) + 1d` c’est la date à laquelle Cousteau a eu un jour.

* On peut `timespan` diviser deux valeurs pour obtenir leur quotient.
  Par `1d / 5h` exemple, `4.8`donne .
  Cela donne à l’un la possibilité d’exprimer n’importe quelle `timespan` valeur comme un multiple d’une autre `timespan` valeur. Par exemple, pour exprimer une heure `1h` `1s`en `1h / 1s` quelques secondes, `3600`il suffit de diviser par : (avec le résultat évident, ).

* Inversement, on peut multipler `double` une `long`valeur `timespan` numérique (comme `timespan` et ) par une valeur pour obtenir une valeur.
  Par exemple, on peut exprimer une `1.5 * 1h`heure et demie comme .

## <a name="example-unix-time"></a>Exemple : Heure Unix

[Unix time](https://en.wikipedia.org/wiki/Unix_time) (également connu sous le nom de HEURE POSIX ou heure unIX) est un système pour décrire un point dans le temps comme le nombre de secondes qui se sont écoulées depuis 00:00:00 Jeudi, 1 Janvier 1970, Coordinated Universal Time (UTC), moins secondes saut.

Si vos données incluent la représentation du temps Unix en tant qu’intégrant, ou si vous avez besoin d’y être convertie, les fonctions suivantes sont disponibles :

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
> En plus des fonctions ci-dessus, voir des fonctions dédiées pour les conversions de temps unix-époque: [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md)
> [unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md)
> [unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)
> [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)