---
title: format_timespan ()-Azure Explorateur de données
description: Cet article décrit format_timespan () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 923008d05ebc8c51a39955e29450e55af4100941
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347950"
---
# <a name="format_timespan"></a>format_timespan()

Met en forme une valeur TimeSpan selon le format fourni.

```kusto
format_timespan(time(14.02:03:04.12345), 'h:m:s.fffffff') == "2:3:4.1234500"
```

## <a name="syntax"></a>Syntaxe

`format_timespan(`*intervalle* `,` de *format*`)`

## <a name="arguments"></a>Arguments

* `timespan`: valeur d’un type `timespan` .
* `format`: chaîne de spécificateur de format, composée d’un ou de plusieurs [éléments de format](#supported-formats).

## <a name="returns"></a>Retourne

Chaîne avec le résultat de format.

## <a name="supported-formats"></a>Formats pris en charge

|Spécificateur de format   |Description    |Exemples
|---|---|---
|`d`-`dddddddd` |Nombre de jours entiers dans l’intervalle de temps. Rempli avec des zéros si nécessaire.|   15.13:45:30 : d-> 15, DD-> 15, DDD-> 015
|`f`    |Dixièmes de seconde dans l’intervalle de temps. |15.13:45:30.6170000-> 6, 15.13:45:30.05-> 0
|`ff`   |Centièmes de seconde dans l’intervalle de temps. |15.13:45:30.6170000-> 61, 15.13:45:30.0050000-> 00
|`fff`  |Millisecondes dans l’intervalle de temps. |6/15/2009 13:45:30.617-> 617, 6/15/2009 13:45:30.0005-> 000
|`ffff` |Dix millièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175000-> 6175, 15.13:45:30.0000500-> 0000
|`fffff`    |Cent millièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175400-> 61754, 15.13:45:30.000005-> 00000
|`ffffff`   |Millionièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175420-> 617542, 15.13:45:30.0000005-> 000000
|`fffffff`  |Dix millionièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175425-> 6175425, 15.13:45:30.0001150-> 0001150
|`F`    |Si la valeur est différente de zéro, dixièmes de seconde dans l’intervalle de temps. |15.13:45:30.6170000-> 6, 15.13:45:30.0500000-> (aucune sortie)
|`FF`   |Si la valeur est différente de zéro, centièmes de seconde dans l’intervalle de temps. |15.13:45:30.6170000-> 61, 15.13:45:30.0050000-> (aucune sortie)
|`FFF`  |Si la valeur est différente de zéro, millisecondes dans l’intervalle de temps. |15.13:45:30.6170000-> 617, 15.13:45:30.0005000-> (aucune sortie)
|`FFFF` |Si la valeur est différente de zéro, il s’agit des dix millièmes de seconde de l’intervalle de temps. |15.13:45:30.5275000-> 5275, 15.13:45:30.0000500-> (aucune sortie)
|`FFFFF`    |Si la valeur est différente de zéro, il s’agit de cent millièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175400-> 61754, 15.13:45:30.0000050-> (aucune sortie)
|`FFFFFF`   |Si la valeur est différente de zéro, elle est le millionième de seconde dans l’intervalle de temps. |15.13:45:30.6175420-> 617542, 15.13:45:30.0000005-> (aucune sortie)
|`FFFFFFF`  |Si la valeur est différente de zéro, dix millionièmes de seconde dans l’intervalle de temps. |15.13:45:30.6175425-> 6175425, 15.13:45:30.0001150-> 000115
|`h`    |Nombre d’heures entières dans l’intervalle de temps qui ne sont pas comptabilisées dans des jours. Les heures à un chiffre n’ont pas de zéro non significatif. |15.01:45:30-> 1, 15.13:45:30-> 1
|`hh`   |Nombre d’heures entières dans l’intervalle de temps qui ne sont pas comptabilisées dans des jours. Les heures à un chiffre ont un zéro non significatif. |15.01:45:30-> 01, 15.13:45:30-> 01
|`H`    |Heure, au format de 24 heures, de 0 à 23. |15.01:45:30-> 1, 15.13:45:30-> 13
|`HH`   |Heure, au format de 24 heures, de 00 à 23. |15.01:45:30-> 01, 15.13:45:30-> 13
|`m`    |Nombre de minutes entières dans l’intervalle de temps qui ne sont pas incluses dans des jours ou des heures. Les minutes à un chiffre n’ont pas de zéro non significatif. |15.01:09:30-> 9, 15.13:29:30-> 29
|`mm`   |Nombre de minutes entières dans l’intervalle de temps qui ne sont pas incluses dans des jours ou des heures. Les minutes à un chiffre ont un zéro non significatif. |15.01:09:30-> 09, 15.01:45:30-> 45
|`s`    |Nombre de secondes entières dans l’intervalle de temps qui ne sont pas incluses dans des jours, heures ou minutes. Les secondes à un chiffre n’ont pas de zéro non significatif. |15.13:45:09-> 9
|`ss`   |Nombre de secondes entières dans l’intervalle de temps qui ne sont pas incluses dans des jours, heures ou minutes. Les secondes à un chiffre ont un zéro non significatif. |15.13:45:09-> 09

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
let t = time(29.09:00:05.12345);
print 
v1=format_timespan(t, 'dd.hh:mm:ss:FF'),
v2=format_timespan(t, 'ddd.h:mm:ss [fffffff]')
```

|v1|v2|
|---|---|
|29.09:00:05:12|029.9:00:05 [1234500]|
