---
title: plug-in active_users_count-Azure Explorateur de données
description: Cet article décrit active_users_count plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b40ca669df7671b1451166f6bfc1c7c680713166
ms.sourcegitcommit: 1f50c6688a2b8d8a3976c0cd0ef40cde2ef76749
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84202957"
---
# <a name="active_users_count-plugin"></a>plug-in active_users_count

Calcule le nombre de valeurs distinctes, où chaque valeur est apparue dans au moins un nombre minimal de périodes dans une période de lookback.

Utile pour le calcul de nombres distincts de « ventilateurs » uniquement, sans inclure les apparences des « non-ventilateurs ». Un utilisateur est compté comme « fan » uniquement s’il était actif pendant la période lookback. La période lookback est utilisée uniquement pour déterminer si un utilisateur est considéré `active` (« fan ») ou non. L’agrégation elle-même n’inclut pas les utilisateurs de la fenêtre lookback. En comparaison, l’agrégation [sliding_window_counts](sliding-window-counts-plugin.md) est effectuée sur une fenêtre glissante de la période lookback.

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

**Syntaxe**

*T* `| evaluate` `active_users_count(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *end* `,` *LookbackWindow* `,` *period* `,` *ActivePeriodsCount* `,` *bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

**Arguments**

* *T*: expression tabulaire d’entrée.
* *IdColumn*: nom de la colonne avec des valeurs d’ID qui représentent l’activité de l’utilisateur. 
* *TimelineColumn*: nom de la colonne qui représente la chronologie.
* *Start*: (facultatif) scalaire avec la valeur de la période de démarrage de l’analyse.
* *End*: (facultatif) scalaire avec la valeur de la période de fin de l’analyse.
* *LookbackWindow*: fenêtre de temps glissante définissant une période dans laquelle l’apparence de l’utilisateur est vérifiée. La période de lookback commence à ([apparence actuelle]-[fenêtre lookback]) et se termine le ([apparence actuelle]). 
* *Période*: valeur scalaire constante TimeSpan à compter comme apparence unique (un utilisateur est compté comme actif s’il apparaît dans au moins un ActivePeriodsCount distinct de ce TimeSpan.
* *ActivePeriodsCount*: nombre minimal de périodes actives distinctes pour décider si l’utilisateur est actif. Les utilisateurs actifs sont ceux qui ont été affichés au moins (égal ou supérieur à) nombre de périodes actives.
* *Bin*: valeur constante scalaire de la période de l’étape d’analyse. Peut être une valeur numérique/DateTime/timestamp, ou une chaîne qui est `week` / `month` / `year` . Toutes les périodes seront les fonctions [startOfWeek](startofweekfunction.md) / [StartOfMonth](startofmonthfunction.md) / [STARTOFYEAR](startofyearfunction.md) correspondantes.
* *dim1*, *dim2*,... : (facultatif) liste des colonnes de dimensions qui découpent le calcul des métriques d’activité.

**Retourne**

Retourne une table qui a des valeurs de comptage de valeurs pour les ID qui sont apparues dans ActivePeriodCounts au cours des périodes suivantes : la période lookback, chaque période de chronologie et chaque combinaison de dimensions existante.

Le schéma de la table de sortie est le suivant :

|*TimelineColumn*|dim1|..|dim_n|dcount_values|
|---|---|---|---|---|
|type : à partir de *TimelineColumn*|..|..|..|long|


**Exemples**

Calculez le nombre hebdomadaire d’utilisateurs distincts qui apparaissaient dans au moins trois jours différents sur une période de huit jours précédents. Période d’analyse : 2018 juillet.

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

|Timestamp|`dcount`|
|---|---|
|2018-07-01 00:00:00.0000000|1|
|2018-07-15 00:00:00.0000000|1|

Un utilisateur est considéré comme actif si les deux critères suivants sont remplis : 
* L’utilisateur s’est vu dans au moins trois jours distincts (période = 1J, ActivePeriods = 3).
* L’utilisateur s’est vu dans une fenêtre lookback de 8D avant et y compris son apparence actuelle.

Dans l’illustration ci-dessous, les seules apparences actives par ce critère sont les suivantes : utilisateur A sur 7/20 et utilisateur B sur 7/4 (voir les résultats du plug-in ci-dessus). Les apparences de l’utilisateur B sont incluses pour la fenêtre lookback sur 7/4, mais pas pour l’intervalle de début de fin de 6/29-30. 

:::image type="content" source="images/queries/active-users-count.png" alt-text="Exemple de nombre d’utilisateurs actifs":::
