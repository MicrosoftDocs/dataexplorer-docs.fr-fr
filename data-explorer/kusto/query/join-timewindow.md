---
title: Jointure dans une fenêtre de temps-Azure Explorateur de données
description: Cet article décrit la jointure dans une fenêtre de temps dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1ca7f38fa377be40cd290b04af65cc6fd59075cd
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763711"
---
# <a name="time-window-join"></a>Jointure de fenêtre de temps

Il est souvent utile de joindre deux jeux de données volumineux sur une clé de cardinalité élevée, par exemple, un ID d’opération ou un ID de session, et limitent davantage les enregistrements de droite ($right) qui doivent correspondre à chaque enregistrement de gauche ($left) en ajoutant une restriction sur la « durée de la distance » entre les colonnes DateTime à gauche et à droite.

La fonction est utile dans une jointure, comme dans le scénario suivant :
* Jointure entre deux grands jeux de données en fonction d’une clé de cardinalité élevée, telle qu’un ID d’opération ou un ID de session.
* Limitez les enregistrements de droite ($right) qui doivent correspondre à chaque enregistrement de gauche ($left), en ajoutant une restriction sur la « durée de la distance » entre les colonnes DateTime à gauche et à droite.

L’opération ci-dessus diffère de l’opération de jointure Kusto habituelle, car pour la mise en *`equi-join`* correspondance de la clé de cardinalité élevée entre les jeux de données de gauche et de droite, le système peut également appliquer une fonction de distance et l’utiliser pour accélérer considérablement la jointure.

> [!NOTE]
> Une fonction distance ne se comporte pas comme l’égalité (c’est-à-dire que la dist (x, y) et la dist (y, z) sont vraies, elle ne suit pas la Loi (x, z) est également vraie.) En interne, nous faisons parfois référence à This en tant que « jointure diagonale ».

Par exemple, si vous souhaitez identifier les séquences d’événements dans une fenêtre de temps relativement courte, supposez que vous avez une table `T` avec le schéma suivant :

* `SessionId`: Colonne de type `string` avec ID de corrélation.
* `EventType`: Colonne de type `string` qui identifie le type d’événement de l’enregistrement.
* `Timestamp`: Une colonne de type `datetime` indique quand l’événement décrit par l’enregistrement s’est produit.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

Notre requête doit répondre à la question suivante :

   Recherche tous les ID de session dans lesquels le type d’événement `A` a été suivi par un type d’événement `B` dans une `1min` fenêtre de temps.

> [!NOTE]
> Dans l’exemple de données ci-dessus, le seul ID de session de ce type est `0` .

Sémantiquement, la requête suivante répond à cette question, bien qu’elle soit inefficace.

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

Pour optimiser cette requête, nous pouvons la réécrire comme indiqué ci-dessous afin que la fenêtre de temps soit exprimée sous la forme d’une clé de jointure.

**Réécrire la requête pour tenir compte de la fenêtre de temps**

Réécrivez la requête afin que les `datetime` valeurs soient « discrètes » dans les compartiments dont la taille est la moitié de la taille de la fenêtre de temps. Utilisez Kusto *`equi-join`* pour comparer ces ID de compartiments.

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

**Référence de requête exécutable (avec table Inline)**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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


**requête de données 50 millions**

La requête suivante émule un jeu de données d’enregistrements 50 millions et environ 10 millions d’ID et exécute la requête avec la technique décrite ci-dessus.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
