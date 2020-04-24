---
title: Le type de données date - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le type de données date time dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5a234c49a5bb5b04ccde49e7fb3702d927e38b5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509909"
---
# <a name="the-datetime-data-type"></a>Le type de données de date

Le `datetime` `date`type de données ( ) représente un instant dans le temps, généralement exprimé comme une date et une heure de la journée.
Les valeurs varient de 00:00:00 (minuit), Janvier 1, 0001 Anno Domini (ère commune) à 23:59:59, Décembre 31, 9999 A.D. (C.E.) dans le calendrier grégorien. 

Les valeurs temporelles sont mesurées en unités de 100 nanosecondes appelées tiques, et une date particulière est le nombre de tiques depuis minuit, 1er janvier 0001 après J.-C. (C.E.) dans le calendrier GregorianCalendar (à l’exclusion des tiques qui seraient ajoutées par des secondes bissextiles).
Par exemple, une valeur de 31241376000000000 représente la date, le vendredi 01 janvier 0100 12:00:00 minuit.
C’est ce qu’on appelle parfois « un moment dans le temps linéaire ».

> [!WARNING]
> Une `datetime` valeur à Kusto est toujours dans le fuseau horaire UTC. Afficher `datetime` des valeurs dans d’autres fuseaux horaires est la responsabilité de l’application utilisateur qui affiche les données, et non une propriété des données elles-mêmes. Si les valeurs du fuseau horaire doivent être conservées dans le cadre des données, une colonne distincte doit être utilisée (fournir des informations offset relatives à l’UTC).

## <a name="datetime-literals"></a>datetime (littéraux)

Les littérals `datetime` de `datetime(`type ont la *valeur*`)`syntaxe , où un certain nombre de formats sont pris en charge pour la *valeur*, comme indiqué par le tableau suivant:

|Exemple                                                     |Valeur                                                         |
|------------------------------------------------------------|--------------------------------------------------------------|
|`datetime(2015-12-31 23:59:59.9)`<br/>`datetime(2015-12-31)`|Les heures sont toujours exprimées en UTC. L’omission de la date entraîne la sélection de la date du jour.|
|`datetime(null)`                                            |Voir [les valeurs nulles](null-values.md).                            |
|`now()`                                                     |L’heure actuelle.                                             |
|`now(`-*Timespan*`)`                                        |`now()-`*Timespan*                                            |
|`ago(`*Timespan*`)`                                         |`now()-`*Timespan*                                            |

`now()`et `ago()` indiquer `datetime` une valeur par rapport au moment où Kusto a commencé à exécuter la requête. Ceux-ci peuvent apparaître plusieurs fois dans la même requête, et une valeur unique sera utilisée pour tous.
(En d’autres termes, des expressions telles que `now(-x) - ago(x)` toujours évaluer à une `timespan` valeur de zéro.)

## <a name="supported-formats"></a>Formats pris en charge

Il existe plusieurs `datetime` formats pour qui sont pris en charge comme [datetime () les littérals](#datetime-literals) et le [todatetime()](../todatetimefunction.md) fonction.

> [!WARNING]
> Il est **fortement recommandé** d’utiliser uniquement les formats ISO 8601.

### <a name="iso-8601"></a>[ISO 8601](https://www.iso.org/iso/home/standards/iso8601.htm)

|Format|Exemple|
|------|-------|
|%Y-%m-%dT%H:%M:%s%z|2014-05-25T08:20:03.123456Z|
|%Y-%m-%dT%H:%M:%s"|2014-05-25T08:20:03.123456|
|%Y-%m-%dT%H:%M"|2014-05-25T08:20|
|%Y-%m-%d %H:%M:%s%z"|2014-11-08 15:55:55.123456Z|
|%Y-%m-%d %H:%M:%s"|2014-11-08 15:55:55|
|%Y-%m-%d %H:%M"|2014-11-08 15:55|
|%Y-%m-%d|2014-11-08|

### <a name="rfc-822"></a>[RFC 822](https://www.ietf.org/rfc/rfc0822.txt)

|Format|Exemple|
|------|-------|
|%w, %e %b %r %H:%M:%s %Z|Sam, 8 Nov 14 15:05:02 GMT|
|%w, %e %b %r %H:%M:%s|Sam, 8 Nov 14 15:05:02|
|%w, %e %b %r %H:%M|Sam, 8 Nov 14 15:05|
|%w, %e %b %r %H:%M %Z|Sam, 8 Nov 14 15:05 GMT|
|%e %b %r %H:%M:%s %Z|8 Nov 14 15:05:02 GMT|
|%e %b %r %H:%M:%s"|8 Nov 14 15:05:02|
|%e %b %r %H:%M"|8 Nov 14 15:05|
|%e %b %r %H:%M %Z"|8 Nov 14 15:05 GMT|

### <a name="rfc-850"></a>[RFC 850](https://tools.ietf.org/html/rfc850)

|Format|Exemple|
|------|-------|
|%w, %e-%b-%r %H:%M:%s %Z|Samedi 08-Nov-14 15:05:02 GMT|
|%w, %e-%b-%r %H:%M:%s|Samedi 08-Nov-14 15:05:02|
|%w, %e-%b-%r %H:%M %Z|Samedi 08-Nov-14 15:05 GMT|
|%w, %e-%b-%r %H:%M|Samedi 08-Nov-14 15:05|
|%e-%b-%r %H:%M:%s %Z|08-Nov-14 15:05:02 GMT|
|%e-%b-%r %H:%M:%s"|08-Nov-14 15:05:02|
|%e-%b-%r %H:%M %Z"|08-Nov-14 15:05 GMT|
|%e-%b-%r %H:%M"|08-Nov-14 15:05|


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
|%Y-%n-%eT%H:%M %Z"|2014-11-08T15:05 GMT|