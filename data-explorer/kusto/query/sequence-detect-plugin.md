---
title: sequence_detect plugin - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit sequence_detect plugin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: afe4b41cc9bf3e81389edfb1fac76472772fa8f6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509178"
---
# <a name="sequence_detect-plugin"></a>sequence_detect plugin

Détecte les événements de séquence en fonction des prédicats fournis.

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

**Syntaxe**

*T* `| evaluate` `,` `,` `,` `,` `,` `,` *TimelineColumn* `,` *Dim2* *Dim1* *Expr2* *Expr1* *MaxSequenceStepWindow* *MaxSequenceSpan* TimelineColumn MaxSequenceStepWindow MaxSequenceSpan Expr1 Expr2 ..., Dim1 Dim2 ... `sequence_detect` `(``)`

**Arguments**

* *T*: L’expression tabulaire d’entrée.
* *TimelineColumn*: référence de colonne représentant la chronologie, doit être présent dans l’expression source
* *MaxSequenceStepWindow*: valeur constante scalaire du temps maximum autorisé entre 2 étapes séquentielles dans la séquence
* *MaxSequenceSpan*: valeur constante scalaire de la portée maximale pour la séquence pour compléter toutes les étapes
* *Expr1*, *Expr2*, ...: boolean expressions prédicat définissant les étapes de séquence
* *Dim1*, *Dim2*, ...: expressions dimension qui sont utilisés pour corréler les séquences

**Retourne**

Renvoie une seule table où chaque rangée dans le tableau représente un événement séquence unique :

* *Dim1*, *Dim2*, ...: colonnes de dimension qui ont été utilisés pour corréler les séquences.
* *Expr1*_*TimelineColumn*, *Expr2*_*TimelineColumn*, ...: Colonnes avec des valeurs de temps, représentant la chronologie de chaque étape de séquence.
* *Durée*: la fenêtre de temps de séquence globale

**Exemples**

### <a name="exploring-storm-events"></a>Explorer les événements orageux 

La requête suivante regarde sur la table StormEvents (statistiques météorologiques pour 2007) et montre les cas où la séquence de «chaleur excessive» a été suivie par «Wildfire» dans les 5 jours.

```kusto
StormEvents
| evaluate sequence_detect(
        StartTime,
        5d,  // step max-time
        5d,  // sequence max-time
        heat=(EventType == "Excessive Heat"), 
        wildfire=(EventType == 'Wildfire'), 
        State)
```

|State|heat_StartTime|wildfire_StartTime|Duration|
|---|---|---|---|
|Californie|2007-05-08 00:00:00.0000000|2007-05-08 16:02:00.0000000|16:02:00|
|Californie|2007-05-08 00:00:00.0000000|2007-05-10 11:30:00.0000000|2.11:30:00|
|Californie|2007-07-04 09:00:00.0000000|2007-07-05 23:01:00.0000000|1.14:01:00|
|DAKOTA DU SUD|2007-07-23 12:00:00.0000000|2007-07-27 09:00:00.0000000|3.21:00:00|
|TEXAS|2007-08-10 08:00:00.0000000|2007-08-11 13:56:00.0000000|1.05:56:00|
|Californie|2007-08-31 08:00:00.0000000|2007-09-01 11:28:00.0000000|1.03:28:00|
|Californie|2007-08-31 08:00:00.0000000|2007-09-02 13:30:00.0000000|2.05:30:00|
|Californie|2007-09-02 12:00:00.0000000|2007-09-02 13:30:00.0000000|01:30:00|