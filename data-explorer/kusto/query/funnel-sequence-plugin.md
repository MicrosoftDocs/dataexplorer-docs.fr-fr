---
title: funnel_sequence plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit funnel_sequence plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0543d0083fd90d5ee3e6e94cf1ee203f6cbd50d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514737"
---
# <a name="funnel_sequence-plugin"></a>plug-in funnel_sequence

Calcule le nombre distinct d’utilisateurs qui ont pris une séquence d’états, et la distribution des états précédents/suivants qui ont conduit à / ont été suivis par la séquence. 

```kusto
T | evaluate funnel_sequence(id, datetime_column, startofday(ago(30d)), startofday(now()), 10m, 1d, state_column, dynamic(['S1', 'S2', 'S3']))
```

**Syntaxe**

*T* `| evaluate` `,` *Start* `,` *End* `,` *Sequence* *Step* *IdColumn* `,` *TimelineColumn* *StateColumn*IdColumn TimelineColumn Start End *MaxSequenceStepWindow*, Step , StateColumn , Séquence `funnel_sequence(``)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *IdColum*: référence de colonne, doit être présent dans l’expression source
* *TimelineColumn*: référence de colonne représentant la chronologie, doit être présent dans l’expression source
* *Début*: valeur constante scalaire de la période de début d’analyse
* *Fin*: valeur constante scalaire de la période de fin d’analyse
* *MaxSequenceStepWindow*: valeur constante scalaire du temps maximum autorisé entre 2 étapes séquentielles dans la séquence
* *Étape*: valeur constante scalaire de la période d’étape d’analyse (bin)
* *StateColumn*: référence de colonne représentant l’état, doit être présent dans l’expression source
* *Séquence*: un tableau dynamique constant avec les `StateColumn`valeurs de séquence (les valeurs sont regardées en)

**Retourne**

Retourne 3 tables de sortie, utiles pour la construction d’un diagramme de sankey pour la séquence analysée :

* Tableau #1 - prév-séquence-prochain dcount TimelineColumn: le prév de fenêtre de temps analysée: l’état de prév (peut être vide s’il y avait des utilisateurs qui n’ont eu des événements pour la séquence recherchée, mais pas tous les événements avant elle). 
    suivant: l’état suivant (peut être vide s’il y avait des utilisateurs qui n’ont eu des événements pour la séquence recherchée, mais pas tous les événements qui l’ont suivi). 
    dcount: compte <IdColumn> distinct de la fenêtre de temps qui a <Sequence> fait la transition [prev] --> --> [prochaine]. 
    échantillons : un tableau d’identité (à partir de <IdColumn>) correspondant à la séquence de la rangée (un maximum de 128 ids sont retournés). 

* Tableau #2 - prév-séquence dcount TimelineColumn: le prév de fenêtre de temps analysée: l’état de prév (peut être vide s’il y avait des utilisateurs qui n’ont eu des événements pour la séquence recherchée, mais pas tous les événements avant elle). 
    dcount: compte <IdColumn> distinct de la fenêtre de temps qui a <Sequence> fait la transition [prev] --> --> [prochaine]. 
    échantillons : un tableau d’identité (à partir de <IdColumn>) correspondant à la séquence de la rangée (un maximum de 128 ids sont retournés). 

* Tableau #3 - séquence-prochaine dcount TimelineColumn: la fenêtre de temps analysée prochaine: l’état suivant (peut être vide s’il y avait des utilisateurs qui n’ont eu des événements pour la séquence recherchée, mais pas tous les événements qui l’ont suivi). 
    dcount: compte <IdColumn> distinct de la fenêtre de temps qui a <Sequence> fait la transition [prev] --> --> [prochaine]. 
    échantillons : un tableau d’identité (à partir de <IdColumn>) correspondant à la séquence de la rangée (un maximum de 128 ids sont retournés). 


**Exemples**

### <a name="exploring-storm-events"></a>Explorer les événements orageux 

La requête suivante regarde sur la table StormEvents (statistiques météorologiques pour 2007) et montre ce que l’événement se passe avant / après tous les événements Tornado s’est produit en 2007.

```kusto
// Looking on StormEvents statistics: 
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

Le résultat comprend 3 tables :

* Tableau #1 : Toutes les variantes possibles de ce qui s’est passé avant et après la séquence. Par exemple, la deuxième ligne indique qu’il y a eu 87 événements différents qui ont eu la séquence suivante :`Hail` -> `Tornado` -> `Hail`


|StartTime|prev|Suivant|dcount|
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
|2007-01-01 00:00:00.0000000|Nuage d’entonnoir|Vent d’orage|6|
|2007-01-01 00:00:00.0000000||Nuage d’entonnoir|6|
|2007-01-01 00:00:00.0000000||Crue soudaine|6|
|2007-01-01 00:00:00.0000000|Nuage d’entonnoir|Nuage d’entonnoir|6|
|2007-01-01 00:00:00.0000000|Grêle|Crue soudaine|4|
|2007-01-01 00:00:00.0000000|Crue soudaine|Vent d’orage|4|
|2007-01-01 00:00:00.0000000|Grêle|Nuage d’entonnoir|4|
|2007-01-01 00:00:00.0000000|Nuage d’entonnoir|Grêle|4|
|2007-01-01 00:00:00.0000000|Nuage d’entonnoir||4|
|2007-01-01 00:00:00.0000000|Vent d’orage|Nuage d’entonnoir|3|
|2007-01-01 00:00:00.0000000|Pluie abondante|Vent d’orage|2|
|2007-01-01 00:00:00.0000000|Crue soudaine|Nuage d’entonnoir|2|
|2007-01-01 00:00:00.0000000|Crue soudaine|Grêle|2|
|2007-01-01 00:00:00.0000000|Vent fort|Vent d’orage|1|
|2007-01-01 00:00:00.0000000|Pluie abondante|Crue soudaine|1|
|2007-01-01 00:00:00.0000000|Pluie abondante|Grêle|1|
|2007-01-01 00:00:00.0000000|Grêle|Crue|1|
|2007-01-01 00:00:00.0000000|Foudre|Grêle|1|
|2007-01-01 00:00:00.0000000|Pluie abondante|Foudre|1|
|2007-01-01 00:00:00.0000000|Nuage d’entonnoir|Pluie abondante|1|
|2007-01-01 00:00:00.0000000|Crue soudaine|Crue|1|
|2007-01-01 00:00:00.0000000|Crue|Crue soudaine|1|
|2007-01-01 00:00:00.0000000||Pluie abondante|1|
|2007-01-01 00:00:00.0000000|Nuage d’entonnoir|Foudre|1|
|2007-01-01 00:00:00.0000000|Foudre|Vent d’orage|1|
|2007-01-01 00:00:00.0000000|Crue|Vent d’orage|1|
|2007-01-01 00:00:00.0000000|Grêle|Foudre|1|
|2007-01-01 00:00:00.0000000||Foudre|1|
|2007-01-01 00:00:00.0000000|Tempête tropicale|Ouragan (Typhon)|1|
|2007-01-01 00:00:00.0000000|Inondation côtière||1|
|2007-01-01 00:00:00.0000000|Courant Rip||1|
|2007-01-01 00:00:00.0000000|Neige lourde||1|
|2007-01-01 00:00:00.0000000|Vent fort||1|

* Tableau #2 : montrer tous les événements distincts regroupés par événement précédent. Par exemple, la deuxième ligne montre qu’il `Hail` y a eu au total 150 événements`Tornado`

|StartTime|prev|Dcount (Dcount)|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||331|
|2007-01-01 00:00:00.0000000|Grêle|150|
|2007-01-01 00:00:00.0000000|Vent d’orage|135|
|2007-01-01 00:00:00.0000000|Crue soudaine|28|
|2007-01-01 00:00:00.0000000|Nuage d’entonnoir|22|
|2007-01-01 00:00:00.0000000|Pluie abondante|5|
|2007-01-01 00:00:00.0000000|Crue|2|
|2007-01-01 00:00:00.0000000|Foudre|2|
|2007-01-01 00:00:00.0000000|Vent fort|2|
|2007-01-01 00:00:00.0000000|Neige lourde|1|
|2007-01-01 00:00:00.0000000|Courant Rip|1|
|2007-01-01 00:00:00.0000000|Inondation côtière|1|
|2007-01-01 00:00:00.0000000|Tempête tropicale|1|

* Table #3 : montrer tous les événements distincts regroupés par le prochain événement. Par exemple, la deuxième ligne montre qu’il `Hail` y a eu au total 143 événements`Tornado`

|StartTime|Suivant|Dcount (Dcount)|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||332|
|2007-01-01 00:00:00.0000000|Grêle|145|
|2007-01-01 00:00:00.0000000|Vent d’orage|143|
|2007-01-01 00:00:00.0000000|Crue soudaine|32|
|2007-01-01 00:00:00.0000000|Nuage d’entonnoir|21|
|2007-01-01 00:00:00.0000000|Foudre|4|
|2007-01-01 00:00:00.0000000|Pluie abondante|2|
|2007-01-01 00:00:00.0000000|Crue|2|
|2007-01-01 00:00:00.0000000|Ouragan (Typhon)|1|

Maintenant, essayons de savoir comment la prochaine séquence se poursuit:  
`Hail` -> `Tornado` -> `Thunderstorm Wind`

```kusto
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, 
dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']))
```

Sauter `Table #1` et `Table #2`, et `Table #3` regarder - nous `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` pouvons conclure que la séquence en `Hail` 92 événements s’est `Tornado` terminée avec cette séquence, a continué comme dans 41 événements, et refoulé à 14.

|StartTime|Suivant|Dcount (Dcount)|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||92|
|2007-01-01 00:00:00.0000000|Grêle|41|
|2007-01-01 00:00:00.0000000|Tornade|14|
|2007-01-01 00:00:00.0000000|Crue soudaine|11|
|2007-01-01 00:00:00.0000000|Foudre|2|
|2007-01-01 00:00:00.0000000|Pluie abondante|1|
|2007-01-01 00:00:00.0000000|Crue|1|