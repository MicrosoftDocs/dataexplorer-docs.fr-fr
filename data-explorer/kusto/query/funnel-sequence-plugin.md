---
title: plug-in funnel_sequence-Azure Explorateur de données
description: Cet article décrit funnel_sequence plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 34159fb6d02cd30907924109c861d5e9fd963568
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252342"
---
# <a name="funnel_sequence-plugin"></a>plug-in funnel_sequence

Calcule le nombre distinct des utilisateurs qui ont pris une séquence d’États, ainsi que la distribution des États précédents/suivants qui ont conduit à/ont été suivis de la séquence. 

```kusto
T | evaluate funnel_sequence(id, datetime_column, startofday(ago(30d)), startofday(now()), 10m, 1d, state_column, dynamic(['S1', 'S2', 'S3']))
```

## <a name="syntax"></a>Syntaxe

*T* `| evaluate` `funnel_sequence(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *end* `,` *MaxSequenceStepWindow*, *Step*, *StateColumn*, *Sequence*`)`

## <a name="arguments"></a>Arguments

* *T*: expression tabulaire d’entrée.
* *IdColum*: référence de colonne, doit être présent dans l’expression source.
* *TimelineColumn*: référence de colonne représentant la chronologie, doit être présente dans l’expression source.
* *Start*: valeur constante scalaire de la période de démarrage de l’analyse.
* *Fin*: valeur constante scalaire de la période de fin de l’analyse.
* *MaxSequenceStepWindow*: valeur constante scalaire de la période maximale autorisée entre deux étapes séquentielles dans la séquence.
* *Étape*: valeur constante scalaire de l’étape d’analyse (bin).
* *StateColumn*: référence de colonne représentant l’État, qui doit être présente dans l’expression source.
* *Sequence*: tableau dynamique constant avec les valeurs de séquence (les valeurs sont recherchées dans `StateColumn` ).

## <a name="returns"></a>Retours

Retourne trois tables de sortie, qui sont utiles pour construire un diagramme Sankey pour la séquence analysée :

* Table #1-préc-Sequence-Next `dcount` TimelineColumn : la fenêtre de temps analysée préc : l’état précédent (peut être vide si des utilisateurs n’avaient qu’un événement pour la séquence recherchée, mais pas les événements antérieurs). 
    suivant : l’état suivant (peut être vide si des utilisateurs avaient uniquement des événements pour la séquence recherchée, mais pas pour les événements qui l’ont suivi). 
    `dcount`: nombre distinct de `IdColumn` dans la fenêtre de temps qui a effectué la transition `prev`  -->  `Sequence`  -->  `next` . 
    exemples : un tableau d’ID (à partir de `IdColumn` ) correspondant à la séquence de la ligne (un maximum de 128 ID est retourné). 

* Table #2-préc-Sequence `dcount` TimelineColumn : la fenêtre de temps analysée préc : l’état précédent (peut être vide si des utilisateurs n’avaient que des événements pour la séquence recherchée, mais pas pour les événements antérieurs à celui-ci). 
    `dcount`: nombre distinct de `IdColumn` dans la fenêtre de temps qui a effectué la transition `prev`  -->  `Sequence`  -->  `next` . 
    exemples : un tableau d’ID (à partir de `IdColumn` ) correspondant à la séquence de la ligne (un maximum de 128 ID est retourné). 

* Table #3-Sequence-Next `dcount` TimelineColumn : la fenêtre temporelle analysée suivante : l’état suivant (peut être vide si des utilisateurs n’avaient qu’un événement pour la séquence recherchée, mais pas les événements qui l’ont suivi). 
    `dcount`: nombre distinct de `IdColumn` dans la fenêtre de temps qui a effectué la transition `prev`  -->  `Sequence`  -->  `next` .
    exemples : un tableau d’ID (à partir de `IdColumn` ) correspondant à la séquence de la ligne (un maximum de 128 ID est retourné). 


## <a name="examples"></a>Exemples

### <a name="exploring-storm-events"></a>Exploration des événements Storm 

La requête suivante examine la table StormEvents (Weather Statistics for 2007) et indique les événements qui se sont produits avant/après que tous les événements tornade se sont produits dans 2007.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Looking on StormEvents statistics: 
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

Le résultat comprend trois tables :

* #1 de table : toutes les variantes possibles de ce qui s’est passé avant et après la séquence. Par exemple, la deuxième ligne signifie qu’il existait 87 événements différents qui se sont dépendants de la séquence suivante : `Hail` -> `Tornado` -> `Hail`


|`StartTime`|`prev`|`next`|`dcount`|
|---|---|---|---|
|2007-01-01 00:00:00.0000000|||293|
|2007-01-01 00:00:00.0000000|Grêle|Grêle|87|
|2007-01-01 00:00:00.0000000|Vent d’orage|Vent d’orage|77|
|2007-01-01 00:00:00.0000000|Grêle|Vent d’orage|28|
|2007-01-01 00:00:00.0000000|Grêle||28|
|2007-01-01 00:00:00.0000000||Grêle|27|
|2007-01-01 00:00:00.0000000||Vent d’orage|25|
|2007-01-01 00:00:00.0000000|Vent d’orage|Grêle|24|
|2007-01-01 00:00:00.0000000|Vent d’orage||24|
|2007-01-01 00:00:00.0000000|Crue soudaine|Crue soudaine|12|
|2007-01-01 00:00:00.0000000|Vent d’orage|Crue soudaine|8|
|2007-01-01 00:00:00.0000000|Crue soudaine||8|
|2007-01-01 00:00:00.0000000|Cloud en entonnoir|Vent d’orage|6|
|2007-01-01 00:00:00.0000000||Cloud en entonnoir|6|
|2007-01-01 00:00:00.0000000||Crue soudaine|6|
|2007-01-01 00:00:00.0000000|Cloud en entonnoir|Cloud en entonnoir|6|
|2007-01-01 00:00:00.0000000|Grêle|Crue soudaine|4|
|2007-01-01 00:00:00.0000000|Crue soudaine|Vent d’orage|4|
|2007-01-01 00:00:00.0000000|Grêle|Cloud en entonnoir|4|
|2007-01-01 00:00:00.0000000|Cloud en entonnoir|Grêle|4|
|2007-01-01 00:00:00.0000000|Cloud en entonnoir||4|
|2007-01-01 00:00:00.0000000|Vent d’orage|Cloud en entonnoir|3|
|2007-01-01 00:00:00.0000000|Pluie lourde|Vent d’orage|2|
|2007-01-01 00:00:00.0000000|Crue soudaine|Cloud en entonnoir|2|
|2007-01-01 00:00:00.0000000|Crue soudaine|Grêle|2|
|2007-01-01 00:00:00.0000000|Vent fort|Vent d’orage|1|
|2007-01-01 00:00:00.0000000|Pluie lourde|Crue soudaine|1|
|2007-01-01 00:00:00.0000000|Pluie lourde|Grêle|1|
|2007-01-01 00:00:00.0000000|Grêle|Crue|1|
|2007-01-01 00:00:00.0000000|Orage|Grêle|1|
|2007-01-01 00:00:00.0000000|Pluie lourde|Orage|1|
|2007-01-01 00:00:00.0000000|Cloud en entonnoir|Pluie lourde|1|
|2007-01-01 00:00:00.0000000|Crue soudaine|Crue|1|
|2007-01-01 00:00:00.0000000|Crue|Crue soudaine|1|
|2007-01-01 00:00:00.0000000||Pluie lourde|1|
|2007-01-01 00:00:00.0000000|Cloud en entonnoir|Orage|1|
|2007-01-01 00:00:00.0000000|Orage|Vent d’orage|1|
|2007-01-01 00:00:00.0000000|Crue|Vent d’orage|1|
|2007-01-01 00:00:00.0000000|Grêle|Orage|1|
|2007-01-01 00:00:00.0000000||Orage|1|
|2007-01-01 00:00:00.0000000|Tempête tropicale|Hurricane (typhon)|1|
|2007-01-01 00:00:00.0000000|Inondation côtière||1|
|2007-01-01 00:00:00.0000000|RIP actuel||1|
|2007-01-01 00:00:00.0000000|Gros neige||1|
|2007-01-01 00:00:00.0000000|Vent fort||1|

* Table #2 : affiche tous les événements distincts regroupés par l’événement précédent. Par exemple, la deuxième ligne indique qu’il y a eu un total de 150 événements de qui se sont produits `Hail` juste avant `Tornado` .

|`StartTime`|`prev`|`dcount`|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||331|
|2007-01-01 00:00:00.0000000|Grêle|150|
|2007-01-01 00:00:00.0000000|Vent d’orage|135|
|2007-01-01 00:00:00.0000000|Crue soudaine|28|
|2007-01-01 00:00:00.0000000|Cloud en entonnoir|22|
|2007-01-01 00:00:00.0000000|Pluie lourde|5|
|2007-01-01 00:00:00.0000000|Crue|2|
|2007-01-01 00:00:00.0000000|Orage|2|
|2007-01-01 00:00:00.0000000|Vent fort|2|
|2007-01-01 00:00:00.0000000|Gros neige|1|
|2007-01-01 00:00:00.0000000|RIP actuel|1|
|2007-01-01 00:00:00.0000000|Inondation côtière|1|
|2007-01-01 00:00:00.0000000|Tempête tropicale|1|

* Table #3 : affiche tous les événements distincts regroupés par événement suivant. Par exemple, la deuxième ligne indique qu’il y a eu un total de 143 événements de qui se sont produits `Hail` après `Tornado` .

|`StartTime`|`next`|`dcount`|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||332|
|2007-01-01 00:00:00.0000000|Grêle|145|
|2007-01-01 00:00:00.0000000|Vent d’orage|143|
|2007-01-01 00:00:00.0000000|Crue soudaine|32|
|2007-01-01 00:00:00.0000000|Cloud en entonnoir|21|
|2007-01-01 00:00:00.0000000|Orage|4|
|2007-01-01 00:00:00.0000000|Pluie lourde|2|
|2007-01-01 00:00:00.0000000|Crue|2|
|2007-01-01 00:00:00.0000000|Hurricane (typhon)|1|

Essayons à présent de savoir comment la séquence suivante continue :  
`Hail` -> `Tornado` -> `Thunderstorm Wind`

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, 
dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']))
```

En ignorant `Table #1` et `Table #2` , et en examinant `Table #3` , nous pouvons conclure que `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` la séquence des événements 92 se termine avec cette séquence, continue comme `Hail` dans 41 événements, et est revenue à `Tornado` l’étape 14.

|`StartTime`|`next`|`dcount`|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||92|
|2007-01-01 00:00:00.0000000|Grêle|41|
|2007-01-01 00:00:00.0000000|Tornade|14|
|2007-01-01 00:00:00.0000000|Crue soudaine|11|
|2007-01-01 00:00:00.0000000|Orage|2|
|2007-01-01 00:00:00.0000000|Pluie lourde|1|
|2007-01-01 00:00:00.0000000|Crue|1|
