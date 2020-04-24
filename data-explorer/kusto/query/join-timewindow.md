---
title: Rejoindre dans les délais - Azure Data Explorer ( Microsoft Docs
description: Cet article décrit Joining within time window in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aa3b81694714ef5af94407cdfdac263af0631e40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513360"
---
# <a name="joining-within-time-window"></a>Rejoindre dans le temps fenêtre

Il est souvent utile de joindre entre deux grands ensembles de données sur une clé de haute cardinalité (comme une pièce d’identité d’opération ou une pièce d’identité de session) et de limiter davantage les enregistrements du côté droit ()`$right`qui doivent être appariés pour chaque enregistrement gauche ()`$left`en ajoutant une restriction sur la « distance de temps » entre `datetime` les colonnes de gauche et de droite. Cela diffère de l’exploitation habituelle Kusto rejoindre car, en plus de la partie "equi-join" (correspondant à la clé haute cardinalité ou les ensembles de données gauche et droite), le système peut également appliquer une fonction de distance et l’utiliser pour accélérer considérablement la jointure. Notez qu’une fonction de distance ne se `dist(x,y)` `dist(y,z)` comporte pas comme l’égalité (c’est-à-dire, quand les deux et sont vraies, il ne suit pas qui `dist(x,z)` est également vrai.) *En interne, nous appelons parfois cela une « jointure diagonale ».*

Supposons, par exemple, que nous voulons identifier les séquences d’événements dans une fenêtre de temps relativement petite. Pour démontrer cet exemple, supposons que nous avons une table `T` avec le schéma suivant:

- `SessionId`: Une colonne `string` de type avec des ID de corrélation.
- `EventType`: Une colonne `string` de type qui identifie le type d’événement de l’enregistrement.
- `Timestamp`: Une colonne `datetime` de type indiquant quand l’événement décrit par le dossier s’est produit.

```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
T
```

|SessionId|Type d’événement|Timestamp|
|---|---|---|
|0|Un|2017-10-01 00:00:00.0000000|
|0|B|2017-10-01 00:01:00.0000000|
|1|B|2017-10-01 00:02:00.0000000|
|1|Un|2017-10-01 00:03:00.0000000|
|3|Un|2017-10-01 00:04:00.0000000|
|3|B|2017-10-01 00:10:00.0000000|


**Définition du problème**

Nous voulons que notre question réponde à la question suivante :

   Trouvez toutes les adresses de `A` session dans lesquelles `B` le `1min` type d’événement a été suivi d’un type d’événement dans le délai.

(Dans les données de l’échantillon `0`ci-dessus, le seul ID de session de ce type est .)

Semantically, la requête suivante répond à cette question, bien que inefficace:

```kusto
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp
    ) on SessionId
| where (End - Start) between (0min .. 1min)
| project SessionId, Start, End 

```

|SessionId|Démarrer|End|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|

Pour optimiser cette requête, nous pouvons la réécrire comme décrit ci-dessous afin que la fenêtre de temps soit exprimée comme une clé de jointure.

**Réécriture de la requête pour tenir compte de la fenêtre de temps**

L’idée est de réécrire la requête afin que les `datetime` valeurs soient « discrètes » en seaux dont la taille est la moitié de la taille de la fenêtre temporelle.
Nous pouvons ensuite utiliser l’équ-join de Kusto pour comparer ces ids seau.

```kusto
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0; // lookup bin = equal to 1/2 of the lookup window
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp,
          // TimeKey on the left side of the join is mapped to a discrete time axis for the join purpose
          TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              // TimeKey on the right side of the join - emulates event 'B' appearing several times
              // as if it was 'replicated'
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    // 'mv-expand' translates the TimeKey array range into a column
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|Démarrer|End|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|


**Référence de requête runnable (avec tableau inlined)**

```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp, TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|Démarrer|End|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|


**Requête de données 50M**

La requête suivante imite l’ensemble de données de 50 millions d’enregistrements et d’ID de 10 millions d’euros et exécute la requête avec la technique décrite ci-dessus.

```kusto
let T = range x from 1 to 50000000 step 1
| extend SessionId = rand(10000000), EventType = rand(3), Time=datetime(2017-01-01)+(x * 10ms)
| extend EventType = case(EventType <= 1, "A",
                          EventType <= 2, "B",
                          "C");
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Time, TimeKey = bin(Time, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Time, 
              TimeKey = range(bin(Time-lookupWindow, lookupBin), 
                              bin(Time, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
| count 
```

|Count|
|---|
|1276|