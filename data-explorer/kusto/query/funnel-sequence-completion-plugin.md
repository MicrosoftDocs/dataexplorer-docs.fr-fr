---
title: plug-in funnel_sequence_completion-Azure Explorateur de données
description: Cet article décrit funnel_sequence_completion plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: cd4d0d07c191d70951c4cfafb549a2bc31f5d153
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249034"
---
# <a name="funnel_sequence_completion-plugin"></a>plug-in funnel_sequence_completion

Calcule l’entonnoir des étapes de séquence terminées dans la comparaison de différentes périodes.

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

## <a name="syntax"></a>Syntaxe

*T* `| evaluate` `funnel_sequence_completion(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Step* `,` *StateColumn* `,` *séquence* `,` *MaxSequenceStepWindows*`)`

## <a name="arguments"></a>Arguments

* *T*: expression tabulaire d’entrée.
* *IdColum*: référence de colonne, doit être présent dans l’expression source.
* *TimelineColumn*: référence de colonne représentant la chronologie, doit être présente dans l’expression source.
* *Start*: valeur constante scalaire de la période de démarrage de l’analyse.
* *Fin*: valeur constante scalaire de la période de fin de l’analyse.
* *Étape*: valeur constante scalaire de l’étape d’analyse (bin).
* *StateColumn*: référence de colonne représentant l’État, qui doit être présente dans l’expression source.
* *Sequence*: tableau dynamique constant avec les valeurs de séquence (les valeurs sont recherchées dans `StateColumn` ).
* *MaxSequenceStepWindows*: tableau dynamique constant scalaire avec les valeurs de la période maximale autorisée entre les première et dernière étapes séquentielles de la séquence. Chaque fenêtre (point) du tableau génère un résultat d’analyse en entonnoir.

## <a name="returns"></a>Retours

Retourne une table unique utile pour construire un diagramme en entonnoir pour la séquence analysée :

* `TimelineColumn`: la fenêtre de temps analysée
* `StateColumn`: état de la séquence.
* `Period`: période maximale (fenêtre) autorisée pour l’exécution des étapes de la séquence de l’entonnoir mesurée à partir de la première étape de la séquence. Chaque valeur de *MaxSequenceStepWindows* génère une analyse en entonnoir avec une période distincte. 
* `dcount`: nombre distinct de `IdColumn` dans la fenêtre de temps qui est passé du premier État de séquence à la valeur de `StateColumn` .

## <a name="examples"></a>Exemples

### <a name="exploring-storm-events"></a>Exploration des événements Storm 

La requête suivante vérifie l’entonnoir d’achèvement de la séquence : `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` dans l’heure « globale » de 1hour, 4hours, 1Day. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let _start = datetime(2007-01-01);
let _end =  datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods) 
```

|`StartTime`|`EventType`|`Period`|`dcount`|
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

Comprendre les résultats :  
Le résultat est trois entonnoirs (pour les périodes : 1 heure, 4 heures et un jour). Pour chaque étape de l’entonnoir, plusieurs nombres distincts de sont affichés. Vous pouvez constater que plus le temps est important pour effectuer l’intégralité de la séquence de `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` , plus la `dcount` valeur est élevée. En d’autres termes, il existait plus d’occurrences de la séquence qui atteignent l’étape entonnoir.
