---
title: Type de données datetime – Azure Data Explorer | Microsoft Docs
description: Cet article décrit le type de données datetime dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: e65b16ac4a280f2ecbef2c4557cac4a8d7be13b3
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513129"
---
# <a name="the-datetime-data-type"></a>Type de données datetime

Le type de données `datetime` (`date`) représente un instant, généralement exprimé sous la forme d’une date et d’une heure de la journée.
Les valeurs sont comprises entre 00:00:00 (minuit), le 1er janvier 0001 de l’ère chrétienne (notre ère) et 11:59:59, le 31 décembre 9999 après J.-C. (notre ère) dans le calendrier grégorien. 

Les valeurs de temps sont mesurées en unités de 100 nanosecondes appelées cycles, et une date particulière est le nombre de cycles depuis le 1er janvier 0001 à minuit de l’ère chrétienne (notre ère) dans le calendrier GregorianCalendar (à l’exception des cycles qui seraient ajoutées par des secondes intercalaires).
Par exemple, la valeur de graduations 31241376000000000 représente la date du vendredi 1er janvier 0100 à minuit (12:00:00).
On parle parfois de « moment dans le temps linéaire ».

> [!WARNING]
> Une valeur de `datetime` dans Kusto est toujours dans le fuseau horaire UTC. L’affichage de valeurs de `datetime` dans d’autres fuseaux horaires relève de la responsabilité de l’application utilisateur qui affiche les données, non d’une propriété des données elles-mêmes. Si des valeurs de fuseau horaire doivent être conservées dans le cadre des données, une colonne séparée doit être utilisée (fournissant des informations de décalage par rapport au fuseau UTC).

## <a name="datetime-literals"></a>datetime (littéraux)

Les littéraux de type `datetime` ont la syntaxe `datetime(`*valeur*`)`, où un certain nombre de formats sont pris en charge pour *valeur*, comme indiqué dans le tableau suivant :

|Exemple                                                     |Valeur                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|Les heures sont toujours exprimées en UTC. L’omission de la date entraîne la sélection de la date du jour.|
|`datetime(null)`                                            |Consultez [Valeurs null](null-values.md).                            |
|`now()`                                                     |L’heure actuelle.                                             |
|`now(`-*timespan*`)`                                        |`now()-`*timespan*                                            |
|`ago(`*timespan*`)`                                         |`now()-`*timespan*                                            |

`now()` et `ago()` indiquent une valeur de `datetime` par rapport au moment où Kusto a commencé l’exécution de la requête. Elles peuvent apparaître plusieurs fois dans la même requête, et une seule valeur sera utilisée pour toutes
(en d’autres termes, des expressions telles que `now(-x) - ago(x)` prennent toujours une valeur `timespan` égale à zéro).

## <a name="supported-formats"></a>Formats pris en charge

Plusieurs formats de `datetime` sont pris en charge en tant que [littéraux datetime()](#datetime-literals) et fonction [todatetime()](../todatetimefunction.md) .

> [!WARNING]
> Il est **fortement recommandé** d’utiliser uniquement les formats ISO 8601.

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|Format|Exemple|
|------|-------|
|%Y-%m-%dT%H:%M:%s%z|2014-05-25T08:20:03.123456Z|
|%Y-%m-%dT%H:%M:%s|2014-05-25T08:20:03.123456|
|%Y-%m-%dT%H:%M|2014-05-25T08:20|
|%Y-%m-%d %H:%M:%s%z|2014-11-08 15:55:55.123456Z|
|%Y-%m-%d %H:%M:%s|2014-11-08 15:55:55|
|%Y-%m-%d %H:%M|2014-11-08 15:55|
|%Y-%m-%d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|Format|Exemple|
|------|-------|
|%w, %e %b %r %H:%M:%s %Z|Sam 8 nov 14 15:05:02 GMT|
|%w, %e %b %r %H:%M:%s|Sam 8 nov 14 15:05:02|
|%w, %e %b %r %H:%M|Sam 8 nov 14 15:05|
|%w, %e %b %r %H:%M %Z|Sam 8 nov 14 15:05 GMT|
|%e %b %r %H:%M:%s %Z|8 nov 14 15:05:02 GMT|
|%e %b %r %H:%M:%s|8 nov 14 15:05:02|
|%e %b %r %H:%M|8 nov 14 15:05|
|%e %b %r %H:%M %Z|8 nov 14 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|Format|Exemple|
|------|-------|
|%w, %e-%b-%r %H:%M:%s %Z|Samedi 08-Nov-14 15:05:02 GMT|
|%w, %e-%b-%r %H:%M:%s|Samedi 08-Nov-14 15:05:02|
|%w, %e-%b-%r %H:%M %Z|Samedi 08-Nov-14 15:05 GMT|
|%w, %e-%b-%r %H:%M|Samedi 08-Nov-14 15:05|
|%e-%b-%r %H:%M:%s %Z|08-Nov-14 15:05:02 GMT|
|%e-%b-%r %H:%M:%s|08-Nov-14 15:05:02|
|%e-%b-%r %H:%M %Z|08-Nov-14 15:05 GMT|
|%e-%b-%r %H:%M|08-Nov-14 15:05|


### <a name="sortable"></a>Triable 

|Format|Exemple|
|------|-------|        
|%Y-%n-%e %H:%M:%s|2014-11-08 15:05:25|
|%Y-%n-%e %H:%M:%s %Z|2014-11-08 15:05:25 GMT|
|%Y-%n-%e %H:%M|2014-11-08 15:05|
|%Y-%n-%e %H:%M %Z|2014-11-08 15:05 GMT|
|%Y-%n-%eT%H:%M:%s|2014-11-08T15:05:25|
|%Y-%n-%eT%H:%M:%s %Z|2014-11-08T15:05:25 GMT|
|%Y-%n-%eT%H:%M|2014-11-08T15:05|
|%Y-%n-%eT%H:%M %Z|2014-11-08T15:05 GMT|
