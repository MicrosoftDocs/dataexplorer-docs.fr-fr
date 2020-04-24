---
title: active_users_count plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit active_users_count plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f324507d1a4528c5efefc14f7820437383211ca6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519344"
---
# <a name="active_users_count-plugin"></a>active_users_count plugin

Calcule un nombre distinct de valeurs, où chaque valeur est apparue dans au moins un nombre minimum de périodes dans une période de retour.

Utile pour calculer les nombres distincts de "fans" seulement, tout en n’incluant pas les apparences de "non-fans". Un utilisateur n’est considéré comme un « fan » que s’il était actif pendant la période de retour. La période de retour n’est utilisée `active` que pour déterminer si un utilisateur est considéré ("fan") ou non. L’agrégation elle-même n’inclut pas les utilisateurs de la fenêtre de retour (contrairement [sliding_window_counts] (sliding-window-counts-plugin.md) dans laquelle l’agrégation est au-dessus de la fenêtre coulissante de la période de retour).

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

**Syntaxe**

*T* `| evaluate` `,` *Start* `,` *End* `,` `,` *Period* `,` `,` *Bin* `,` `,` *dim2* `,` *dim1* *IdColumn* `,` *TimelineColumn* *LookbackWindow* *ActivePeriodsCount* IdColumn TimelineColumn Start End LookbackWindow Période ActivePeriodsCount Bin [ dim1 dim2 ...] `active_users_count(``)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *IdColumn*: Le nom de la colonne avec des valeurs d’identification qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: Le nom de la colonne qui représente la chronologie.
* *Démarrer*: (facultatif) Scalar avec la valeur de la période de début d’analyse.
* *Fin*: (facultatif) Scalar avec valeur de la période de fin d’analyse.
* *LookbackWindow*: Une fenêtre de temps coulissante définissant une période où l’apparence de l’utilisateur est vérifiée. La période de retour commence à ([apparence actuelle] - [fenêtre de retour]) et se termine sur ([apparence actuelle]). 
* *Période*: Scalar temps constant à compter comme apparence unique (un utilisateur sera compté comme actif si elle apparaît dans au moins distinct ActivePeriodsCount de cette période.
* *ActivePeriodsCount*: Nombre minimal de périodes actives distinctes pour décider si l’utilisateur est actif. Les utilisateurs actifs sont ceux qui sont apparus dans au moins (égal ou supérieur à) les périodes actives comptent.
* *Bin*: Valeur constante Scalar de la période d’étape d’analyse. Peut être soit une valeur numérique /datetime/timestamp, `week` / `month` / `year`ou une chaîne qui est l’un des , auquel cas toutes les périodes seront [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md) en conséquence.
* *dim1*, *dim2*, ...: (facultatif) liste des colonnes de dimensions qui tranchent le calcul des mesures d’activité.

**Retourne**

Retourne un tableau qui a les valeurs de comptage distinctes pour les ids qui sont apparus dans plus ActivePeriodCounts dans la période de retour, pour chaque période de chronologie et pour chaque combinaison de dimensions existantes.

Le schéma de table de sortie est :

|*TimelineColumn (en)*|dim1|..|dim_n|dcount_values|
|---|---|---|---|---|
|type: à partir de *TimelineColumn*|..|..|..|long|


**Exemples**

Calculez le nombre hebdomadaire d’utilisateurs distincts qui sont apparus dans au moins sur 3 jours différents sur une période de 8 jours antérieurs. Période d’analyse : juillet 2018.

```kusto
let Start = datetime(2018-07-01);
let End = datetime(2018-07-31);
let LookbackWindow = 8d;
let Period = 1d;
let ActivePeriods = 3;
let Bin = 7d; 
let T =  datatable(User:string, Timestamp:datetime)
[
    "B",      datetime(2018-06-29),
    "B",      datetime(2018-06-30),
    "A",      datetime(2018-07-02),
    "B",      datetime(2018-07-04),
    "B",      datetime(2018-07-08),
    "A",      datetime(2018-07-10),
    "A",      datetime(2018-07-14),
    "A",      datetime(2018-07-17),
    "A",      datetime(2018-07-20),
    "B",      datetime(2018-07-24)
]; 
T | evaluate active_users_count(User, Timestamp, Start, End, LookbackWindow, Period, ActivePeriods, Bin)



```

|Timestamp|dcount|
|---|---|
|2018-07-01 00:00:00.0000000|1|
|2018-07-15 00:00:00.0000000|1|

Un utilisateur est considéré comme actif s’il a été vu dans au moins 3 jours distincts (période 1d, ActivePeriods 3) dans une fenêtre de retour de 8d avant l’apparition actuelle (y compris l’apparence actuelle). Dans l’illustration ci-dessous, les seules apparences qui sont actives selon ces critères, sont l’utilisateur A sur le 7/20 et l’utilisateur B sur le 7/4 (voir les résultats plugin ci-dessus). Notez que bien que les apparences de l’utilisateur B sur 6/29-30 ne sont pas dans la plage de temps de début de fin, ils sont inclus pour la fenêtre de retour de l’utilisateur B sur le 7/4. 

![texte de remplacement](images/queries/active-users-count.png "actifs-utilisateurs-compte")