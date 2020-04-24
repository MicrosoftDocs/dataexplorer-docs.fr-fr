---
title: funnel_sequence_completion plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit funnel_sequence_completion plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: 7f168841db2df47e4e3a192b75585a1fe4d83718
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514771"
---
# <a name="funnel_sequence_completion-plugin"></a>plug-in funnel_sequence_completion

Calcule l’entonnoir des étapes de séquence terminées en comparant différentes périodes de temps.

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

**Syntaxe**

*T* `| evaluate` `,` *Start* `,` *End* `,` *Step* `,` `,` *Sequence* `,` *IdColumn* `,` *TimelineColumn* *StateColumn* IdColumn TimelineColumn Start End Step StateColumn Séquence *MaxSequenceStepWindows* `funnel_sequence_completion(``)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *IdColum*: référence de colonne, doit être présent dans l’expression source
* *TimelineColumn*: référence de colonne représentant la chronologie, doit être présent dans l’expression source
* *Début*: valeur constante scalaire de la période de début d’analyse
* *Fin*: valeur constante scalaire de la période de fin d’analyse
* *Étape*: valeur constante scalaire de la période d’étape d’analyse (bin) 
* *StateColumn*: référence de colonne représentant l’état, doit être présent dans l’expression source
* *Séquence*: un tableau dynamique constant avec les `StateColumn`valeurs de séquence (les valeurs sont regardées en)
* *MaxSequenceStepWindows*: tableau dynamique constant scalaire avec les valeurs du temps maximum autorisé entre les premières et dernières étapes séquentielles de la séquence, chaque fenêtre (période) du tableau génère un résultat d’analyse d’entonnoir

**Retourne**

Retourne une seule table utile pour la construction d’un diagramme d’entonnoir pour la séquence analysée :

* TimelineColumn: la fenêtre de temps analysée
* `StateColumn`: l’état de la séquence.
* Période : la période maximale (fenêtre) permet de terminer les étapes de la séquence d’entonnoir mesurée à partir de la première étape de la séquence. Chaque valeur dans *MaxSequenceStepWindows* génère une analyse d’entonnoir avec une période distincte. 
* dcount: compte `IdColumn` distinct de la fenêtre de temps qui `StateColumn`est passé de l’état de la première séquence à la valeur de .

**Exemples**

### <a name="exploring-storm-events"></a>Explorer les événements orageux 

La requête suivante vérifie l’entonnoir d’achèvement de la `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` séquence : en temps « global » de 1 heure, 4 heures, 1day. 

```kusto
let _start = datetime(2007-01-01);
let _end =  datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods) 
```

|StartTime|Type d’événement|Période|dcount|
|---|---|---|---|
|2007-01-01 00:00:00.0000000|Grêle|01:00:00|2877|
|2007-01-01 00:00:00.0000000|Tornade|01:00:00|208|
|2007-01-01 00:00:00.0000000|Vent d’orage|01:00:00|87|
|2007-01-01 00:00:00.0000000|Grêle|04:00:00|2877|
|2007-01-01 00:00:00.0000000|Tornade|04:00:00|231|
|2007-01-01 00:00:00.0000000|Vent d’orage|04:00:00|141|
|2007-01-01 00:00:00.0000000|Grêle|1.00:00:00|2877|
|2007-01-01 00:00:00.0000000|Tornade|1.00:00:00|244|
|2007-01-01 00:00:00.0000000|Vent d’orage|1.00:00:00|155|

Comprendre les résultats :  
Le résultat il 3 entonnoirs (pour des périodes: 1 heure, 4 heures, et 1 jour), tandis que pour chaque étape d’entonnoir un certain nombre de nombre distincts d’EpisodeId est montré. Vous pouvez voir que plus il est possible `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` de `dcount` compléter la séquence de la valeur supérieure (ce qui signifie plus d’occurrences de la séquence atteignant l’étape de l’entonnoir).
