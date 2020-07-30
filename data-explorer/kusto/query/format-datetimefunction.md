---
title: format_datetime ()-Azure Explorateur de données
description: Cet article décrit format_datetime () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d0a60a61e5319d9a190f6172b9c0bd8fb7fe1300
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347967"
---
# <a name="format_datetime"></a>format_datetime()

Met en forme une valeur DateTime selon le format fourni.

```kusto
format_datetime(datetime(2015-12-14 02:03:04.12345), 'y-M-d h:m:s.fffffff') == "15-12-14 2:3:4.1234500"
```

## <a name="syntax"></a>Syntaxe

`format_datetime(`*date/heure* `,` *format*`)`

## <a name="arguments"></a>Arguments

* `datetime`: valeur d’un type `datetime` .
* `format`: chaîne de spécificateur de format, composée d’un ou de plusieurs [éléments de format](#supported-formats).

## <a name="returns"></a>Retourne

Chaîne avec le résultat de format.

## <a name="supported-formats"></a>Formats pris en charge

|Spécificateur de format   |Description    |Exemples
|---|---|---
|`d`    |Jour du mois, de 1 à 31. | 2009-06-01T13:45:30-> 1, 2009-06-15T13:45:30-> 15
|`dd`   |Jour du mois, de 01 à 31.| 2009-06-01T13:45:30-> 01, 2009-06-15T13:45:30-> 15
|`f`    |Dixièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6170000-> 6, 2009-06-15T13:45:30.05-> 0
|`ff`   |Centièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6170000-> 61, 2009-06-15T13:45:30.0050000-> 00
|`fff`  |Millisecondes dans une valeur de date et d'heure. |6/15/2009 13:45:30.617-> 617, 6/15/2009 13:45:30.0005-> 000
|`ffff` |Dix millièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6175000-> 6175, 2009-06-15T13:45:30.0000500-> 0000
|`fffff`    |Cent millièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6175400-> 61754, 2009-06-15T13:45:30.000005-> 00000
|`ffffff`   |Millionièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6175420-> 617542, 2009-06-15T13:45:30.0000005-> 000000
|`fffffff`  |Dix millionièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6175425-> 6175425, 2009-06-15T13:45:30.0001150-> 0001150
|`F`    |Si la valeur est différente de zéro, elle représente les dixièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6170000-> 6, 2009-06-15T13:45:30.0500000-> (aucune sortie)
|`FF`   |Si la valeur est différente de zéro, elle représente les centièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6170000-> 61, 2009-06-15T13:45:30.0050000-> (aucune sortie)
|`FFF`  |Si la valeur est différente de zéro, elle représente les millisecondes dans une valeur de date et d'heure. |2009-06-15T13:45:30.6170000-> 617, 2009-06-15T13:45:30.0005000-> (aucune sortie)
|`FFFF` |Si la valeur est différente de zéro, elle représente les dix millièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.5275000-> 5275, 2009-06-15T13:45:30.0000500-> (aucune sortie)
|`FFFFF`    |Si la valeur est différente de zéro, elle représente les cent millièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6175400-> 61754, 2009-06-15T13:45:30.0000050-> (aucune sortie)
|`FFFFFF`   |Si la valeur est différente de zéro, elle représente les millionièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6175420-> 617542, 2009-06-15T13:45:30.0000005-> (aucune sortie)
|`FFFFFFF`  |Si la valeur est différente de zéro, elle représente les dix millionièmes de seconde dans une valeur de date et d'heure. |2009-06-15T13:45:30.6175425-> 6175425, 2009-06-15T13:45:30.0001150-> 000115
|`h`    |Heure, au format de 12 heures, de 1 à 12. |2009-06-15T01:45:30-> 1, 2009-06-15T13:45:30-> 1
|`hh`   |Heure, au format de 12 heures, de 01 à 12. |2009-06-15T01:45:30-> 01, 2009-06-15T13:45:30-> 01
|`H`    |Heure, au format de 24 heures, de 0 à 23. |2009-06-15T01:45:30-> 1, 2009-06-15T13:45:30-> 13
|`HH`   |Heure, au format de 24 heures, de 00 à 23. |2009-06-15T01:45:30-> 01, 2009-06-15T13:45:30-> 13
|`m`    |Minute, définie entre 0 et 59. |2009-06-15T01:09:30-> 9, 2009-06-15T13:29:30-> 29
|`mm`   |Minute, définie entre 00 et 59. |2009-06-15T01:09:30-> 09, 2009-06-15T01:45:30-> 45
|`M`    |Mois, de 1 à 12. |2009-06-15T13:45:30 -> 6
|`MM`   |Mois, de 01 à 12.|2009-06-15T13:45:30 -> 06
|`s`    |Seconde, de 0 à 59. |2009-06-15T13:45:09 -> 9
|`ss`   |Seconde, de 00 à 59. |2009-06-15T13:45:09 -> 09
|`y`    |Année, de 0 à 99. |0001-01-01T00:00:00-> 1, 0900-01-01T00:00:00-> 0, 1900-01-01T00:00:00-> 0, 2009-06-15T13:45:30-> 9, 2019-06-15T13:45:30-> 19
|`yy`   |Année, de 00 à 99. | 0001-01-01T00:00:00-> 01, 0900-01-01T00:00:00-> 00, 1900-01-01T00:00:00-> 00, 2019-06-15T13:45:30-> 19
|`yyyy` |Année, en tant que nombre à quatre chiffres. | 0001-01-01T00:00:00-> 0001, 0900-01-01T00:00:00-> 0900, 1900-01-01T00:00:00-> 1900, 2009-06-15T13:45:30-> 2009
|`tt`   |Heures AM/PM |2009-06-15T13:45:09-> PM

**Séparateurs pris en charge**

Le spécificateur de format peut inclure les caractères séparateurs suivants :

|Délimiteur|Commentaire|
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

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let dt = datetime(2017-01-29 09:00:05);
print 
v1=format_datetime(dt,'yy-MM-dd [HH:mm:ss]'), 
v2=format_datetime(dt, 'yyyy-M-dd [H:mm:ss]'),
v3=format_datetime(dt, 'yy-MM-dd [hh:mm:ss tt]')
```

|v1|v2|v3|
|---|---|---|
|17-01-29 [09:00:05]|2017-1-29 [9:00:05]|17-01-29 [09:00:05 AM]|
