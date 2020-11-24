---
title: Le type de données DateTime-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le type de données DateTime dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: e65b16ac4a280f2ecbef2c4557cac4a8d7be13b3
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513129"
---
# <a name="the-datetime-data-type"></a>Le type de données DateTime

Le `datetime` `date` type de données () représente un instant, généralement exprimé sous la forme d’une date et d’une heure.
Les valeurs sont comprises entre le 00:00:00 (minuit), le 1er janvier 0001 Anno et (ère commune) 11:59:59 et le 31 décembre 9999 après J.-C. commune dans le calendrier grégorien. 

Les valeurs d’heure sont mesurées en unités de 100 nanosecondes appelées cycles, et une date particulière est le nombre de cycles 12:00 depuis le 1er janvier 0001 à minuit commune dans le calendrier GregorianCalendar (à l’exception des graduations qui seraient ajoutées par les secondes bissextiles).
Par exemple, la valeur des graduations de 31241376000000000 représente la date, le vendredi 1er janvier 0100 12:00:00 minuit.
Cela est parfois appelé « un moment en temps linéaire ».

> [!WARNING]
> Une `datetime` valeur dans Kusto est toujours dans le fuseau horaire UTC. L’affichage des `datetime` valeurs dans d’autres fuseaux horaires est la responsabilité de l’application utilisateur qui affiche les données, et non pas d’une propriété des données elle-même. Si les valeurs de fuseau horaire doivent être conservées dans le cadre des données, une colonne séparée doit être utilisée (en fournissant des informations de décalage par rapport à l’heure UTC).

## <a name="datetime-literals"></a>datetime (littéraux)

Les littéraux de type `datetime` ont la `datetime(` *valeur* de syntaxe `)` , où un certain nombre de formats sont pris en charge pour la *valeur*, comme indiqué dans le tableau suivant :

|Exemple                                                     |Valeur                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|Les heures sont toujours exprimées en UTC. L’omission de la date entraîne la sélection de la date du jour.|
|`datetime(null)`                                            |Consultez [valeurs NULL](null-values.md).                            |
|`now()`                                                     |L’heure actuelle.                                             |
|`now(`-*TimeSpan*`)`                                        |`now()-`*TimeSpan*                                            |
|`ago(`*TimeSpan*`)`                                         |`now()-`*TimeSpan*                                            |

`now()` et `ago()` indiquent une `datetime` valeur par rapport au moment où Kusto a commencé l’exécution de la requête. Elles peuvent apparaître plusieurs fois dans la même requête, et une seule valeur sera utilisée pour toutes.
(En d’autres termes, les expressions telles que `now(-x) - ago(x)` donnent toujours la `timespan` valeur zéro.)

## <a name="supported-formats"></a>Formats pris en charge

Il existe plusieurs formats pour `datetime` qui sont pris en charge en tant que [littéraux DateTime ()](#datetime-literals) et la fonction [ToDateTime ()](../todatetimefunction.md) .

> [!WARNING]
> Il est **fortement recommandé** d’utiliser uniquement les formats ISO 8601.

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|Format|Exemple|
|------|-------|
|% Y-% m-% dT% H :%M :% s% z|2014-05-25T08:20:03.123456 Z|
|% Y-% m-% dT% H :%M :% s|2014-05-25T08:20:03.123456|
|% Y-% m-% dT% H :%M|2014-05-25T08:20|
|% Y-% m-% d% H :%M :% s% z|2014-11-08 15:55:55.123456 z|
|% Y-% m-% d% H :%M :% s|2014-11-08 15:55:55|
|% Y-% m-% d% H :%M|2014-11-08 15:55|
|% Y-% m-% d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|Format|Exemple|
|------|-------|
|% w,% e% b% r% H :%M :% s% Z|Sat, 8 novembre 14 15:05:02 GMT|
|% w,% e% b% r% H :%M :% s|Sat, 8 novembre 14 15:05:02|
|% w,% e% b% r% H :%M|Sat, 8 novembre 14 15:05|
|% w,% e% b% r% H :%M% Z|Sat, 8 novembre 14 15:05 GMT|
|% e% b% r% H :%M :% s% Z|8 novembre 14 15:05:02 GMT|
|% e% b% r% H :%M :% s|8 novembre 14 15:05:02|
|% e% b% r% H :%M|8 novembre 14 15:05|
|% e% b% r% H :%M% Z|8 novembre 14 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|Format|Exemple|
|------|-------|
|% w,% e-% b-% r% H :%M :% s% Z|Samedi, 08-Nov-14 15:05:02 GMT|
|% w,% e-% b-% r% H :%M :% s|Samedi, 08-Nov-14 15:05:02|
|% w,% e-% b-% r% H :%M% Z|Samedi, 08-Nov-14 15:05 GMT|
|% w,% e-% b-% r% H :%M|Samedi, 08-Nov-14 15:05|
|% e-% b-% r% H :%M :% s% Z|08-Nov-14 15:05:02 GMT|
|% e-% b-% r% H :%M :% s|08-Nov-14 15:05:02|
|% e-% b-% r% H :%M% Z|08-Nov-14 15:05 GMT|
|% e-% b-% r% H :%M|08-Nov-14 15:05|


### <a name="sortable"></a>Triable 

|Format|Exemple|
|------|-------|        
|% Y-% n-% e% H :%M :% s|2014-11-08 15:05:25|
|% Y-% n-% e% H :%M :% s% Z|2014-11-08 15:05:25 GMT|
|% Y-% n-% e% H :%M|2014-11-08 15:05|
|% Y-% n-% e% H :%M% Z|2014-11-08 15:05 GMT|
|% Y-% n-% eT% H :%M :% s|2014-11-08T15:05:25|
|% Y-% n-% eT% H :%M :% s% Z|2014-11-08T15:05:25 GMT|
|% Y-% n-% eT% H :%M|2014-11-08T15:05|
|% Y-% n-% eT% H :%M% Z|2014-11-08T15:05 GMT|
