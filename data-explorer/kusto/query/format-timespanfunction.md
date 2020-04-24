---
title: format_timespan() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit format_timespan() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2cc894d3b10cd3e4badd1ff7b498c23618c76818
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514992"
---
# <a name="format_timespan"></a>format_timespan()

Formats une durée de temps selon le format fourni.

```kusto
format_timespan(time(14.02:03:04.12345), 'h:m:s.fffffff') == "2:3:4.1234500"
```

**Syntaxe**

`format_timespan(`*format* *timespan* `,``)`

**Arguments**

* `timespan`: valeur d’un type `timespan`.
* `format`: chaîne de spécificateur de format, composée d’un ou plusieurs [éléments de format.](#supported-formats)

**Retourne**

La chaîne avec le résultat du format.

## <a name="supported-formats"></a>Formats pris en charge

|Spécificateur de format   |Description    |Exemples
|---|---|---
|`d`-`dddddddd` |Nombre de jours entiers dans l’intervalle de temps. Rembourré avec des zéros si nécessaire.|   15.13:45:30: d -> 15, dd -> 15, ddd -> 015
|`f`    |Les dixièmes de seconde dans l’intervalle de temps. |15.13:45:30.6170000 -> 6, 15.13:45:30.05 -> 0
|`ff`   |Les centièmes de seconde dans l’intervalle de temps. |15.13:45:30.6170000 -> 61, 15.13:45:30.0050000 -> 00
|`fff`  |Les millisecondes dans l’intervalle de temps. |15/06/2009 13:45:30.617 -> 617, 15/06/2009 13:45:30.0005 -> 000
|`ffff` |Les dix millièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175000 -> 6175, 15.13:45:30.0000500 -> 0000
|`fffff`    |Les cent millièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175400 -> 61754, 15.13:45:30.000005 -> 00000
|`ffffff`   |Les millionièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175420 -> 617542, 15.13:45:30.0000005 -> 0000000
|`fffffff`  |Les dix millionièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175425 -> 6175425, 15.13:45:30.0001150 -> 0001150
|`F`    |Si non-zéro, les dixièmes de seconde dans l’intervalle de temps. |15.13:45:30.6170000 -> 6, 15.13:45:30.0500000 -> (pas de sortie)
|`FF`   |Si non-zéro, les centièmes de seconde dans l’intervalle de temps. |15.13:45:30.6170000 -> 61, 15.13:45:30.0050000 -> (pas de sortie)
|`FFF`  |Si non-zéro, les millisecondes dans l’intervalle de temps. |15.13:45:30.6170000 -> 617, 15.13:45:30.0005000 -> (pas de sortie)
|`FFFF` |Si non-zéro, les dix millièmes de seconde dans l’intervalle de temps. |15.13:45:30.5275000 -> 5275, 15.13:45:30.0000500 -> (pas de sortie)
|`FFFFF`    |Si non-zéro, les cent millièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175400 -> 61754, 15.13:45:30.0000050 -> (pas de sortie)
|`FFFFFF`   |Si non-zéro, les millionièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175420 -> 617542, 15.13:45:30.0000005 -> (pas de sortie)
|`FFFFFFF`  |Si non-zéro, les dix millionièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175425 -> 6175425, 15.13:45:30.0001150 -> 000115
|`h`    |Nombre d’heures entières dans l’intervalle de temps qui ne sont pas comptabilisées dans des jours. Les heures à un chiffre n’ont pas de zéro non significatif. |15.01:45:30 -> 1, 15.13:45:30 -> 1
|`hh`   |Nombre d’heures entières dans l’intervalle de temps qui ne sont pas comptabilisées dans des jours. Les heures à un chiffre ont un zéro non significatif. |15.01:45:30 -> 01, 15.13:45:30 -> 01
|`H`    |Heure, au format de 24 heures, de 0 à 23. |15.01:45:30 -> 1, 15.13:45:30 -> 13
|`HH`   |Heure, au format de 24 heures, de 00 à 23. |15.01:45:30 -> 01, 15.13:45:30 -> 13
|`m`    |Nombre de minutes entières dans l’intervalle de temps qui ne sont pas incluses dans des jours ou des heures. Les minutes à un chiffre n’ont pas de zéro non significatif. |15.01:09:30 -> 9, 15.13:29:30 -> 29
|`mm`   |Nombre de minutes entières dans l’intervalle de temps qui ne sont pas incluses dans des jours ou des heures. Les minutes à un chiffre ont un zéro non significatif. |15.01:09:30 -> 09, 15.01:45:30 -> 45
|`s`    |Nombre de secondes entières dans l’intervalle de temps qui ne sont pas incluses dans des jours, heures ou minutes. Les secondes à un chiffre n’ont pas de zéro non significatif. |15.13:45:09 -> 9
|`ss`   |Nombre de secondes entières dans l’intervalle de temps qui ne sont pas incluses dans des jours, heures ou minutes. Les secondes à un chiffre ont un zéro non significatif. |15.13:45:09 -> 09

**Délimitateurs soutenus**

Le spécificateur de format peut inclure les caractères suivants de délimitateurs :

|Delimeter Delimeter|Commentaire|
|---------|-------|
|`' '`| Espace|
|`'/'`||
|`'-'`|Tiret|
|`':'`||
|`','`||
|`'.'`||
|`'_'`||
|`'['`||
|`']'`||

**Exemples**

```kusto
let t = time(29.09:00:05.12345);
print 
v1=format_timespan(t, 'dd.hh:mm:ss:FF'),
v2=format_timespan(t, 'ddd.h:mm:ss [fffffff]')
```

|v1|v2|
|---|---|
|29.09:00:05:12|029.9:00:05 [1234500]|
