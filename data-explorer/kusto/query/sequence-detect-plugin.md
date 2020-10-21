---
title: plug-in sequence_detect-Azure Explorateur de données
description: Cet article décrit sequence_detect plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c284a1044e3e5a48cddeec352a945b48b665ce36
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250226"
---
# <a name="sequence_detect-plugin"></a>sequence_detect, plug-in

Détecte les occurrences de séquence en fonction des prédicats fournis.

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

## <a name="syntax"></a>Syntaxe

*T* `| evaluate` `sequence_detect` `(` *TimelineColumn* `,` *MaxSequenceStepWindow* `,` *MaxSequenceSpan* `,` *expr1* `,` *expr2* `,` ..., *dim1* `,` *dim2* `,` ...`)`

## <a name="arguments"></a>Arguments

* *T*: expression tabulaire d’entrée.
* *TimelineColumn*: référence de colonne représentant la chronologie, doit être présente dans l’expression source
* *MaxSequenceStepWindow*: valeur constante scalaire de la période maximale autorisée comprise entre 2 étapes séquentielles dans la séquence
* *MaxSequenceSpan*: valeur constante scalaire de l’étendue Max pour la séquence pour effectuer toutes les étapes
* *Expr1*, *expr2*,... : expressions de prédicat booléennes définissant des étapes de séquence
* *Dim1*, *dim2*,... : expressions de dimension utilisées pour corréler des séquences

## <a name="returns"></a>Retours

Retourne une table unique où chaque ligne de la table représente une occurrence de séquence unique :

* *Dim1*, *dim2*,... : colonnes de dimension qui ont été utilisées pour corréler des séquences.
* *Expr1*_*TimelineColumn*, *expr2*_*TimelineColumn*,... : Columns avec des valeurs de temps, représentant la chronologie de chaque étape de séquence.
* *Durée*: fenêtre de temps de séquence globale

## <a name="examples"></a>Exemples

### <a name="exploring-storm-events"></a>Exploration des événements Storm 

La requête suivante examine la table StormEvents (Weather Statistics for 2007) et montre les cas où la séquence de « surchauffe excessive » a été suivie de « feux » dans les 5 jours.

<!-- csl: https://help.kusto.windows.net/Samples -->
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
|France|2007-05-08 00:00:00.0000000|2007-05-08 16:02:00.0000000|16:02:00|
|France|2007-05-08 00:00:00.0000000|2007-05-10 11:30:00.0000000|2.11:30:00|
|France|2007-07-04 09:00:00.0000000|2007-07-05 23:01:00.0000000|1.14:01:00|
|DAKOTA DU SUD|2007-07-23 12:00:00.0000000|2007-07-27 09:00:00.0000000|3.21:00:00|
|TEXAS|2007-08-10 08:00:00.0000000|2007-08-11 13:56:00.0000000|1.05:56:00|
|France|2007-08-31 08:00:00.0000000|2007-09-01 11:28:00.0000000|1.03:28:00|
|France|2007-08-31 08:00:00.0000000|2007-09-02 13:30:00.0000000|2.05:30:00|
|France|2007-09-02 12:00:00.0000000|2007-09-02 13:30:00.0000000|01:30:00|
