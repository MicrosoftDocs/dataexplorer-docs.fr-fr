---
title: matérialiser ()-Azure Explorateur de données
description: Cet article décrit la fonction matérialiser () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/06/2020
ms.openlocfilehash: f5ea896d4701aa5aec1b22c1ff20853aea18f065
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664940"
---
# <a name="materialize"></a>materialize()

Permet de mettre en cache le résultat d’une sous-requête pendant l’exécution de la requête d’une manière que d’autres sous-requêtes peuvent référencer le résultat partiel.
 
**Syntaxe**

`materialize(`*expression*`)`

**Arguments**

* *expression*: expression tabulaire à évaluer et à mettre en cache lors de l’exécution de la requête.

**Conseils**

* Utilisez matérialisation avec JOIN ou Union lorsque leurs opérandes ont des sous-requêtes réciproques qui peuvent être exécutées une seule fois. Consultez les exemples ci-dessous.

* Utile également dans des scénarios lorsque nous avons besoin de branchements join/union.

* Matérialiser ne peut être utilisé que dans les instructions Let si vous attribuez un nom au résultat mis en cache.

**Remarque**

* La matérialisation a une limite de taille de cache de **5 Go**. 
  Cette limite est définie par nœud de cluster et est mutuelle pour toutes les requêtes qui s’exécutent simultanément.
  Si une requête utilise `materialize()` et que le cache ne peut pas contenir plus de données, la requête s’interrompt avec une erreur.

**Exemples**

L’exemple suivant montre comment `materialize()` peut être utilisé pour améliorer les performances de la requête.
L’expression `_detailed_data` est définie à l’aide `materialize()` de la fonction et, par conséquent, elle n’est calculée qu’une seule fois.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _detailed_data = materialize(StormEvents | summarize Events=count() by State, EventType);
_detailed_data
| summarize TotalStateEvents=sum(Events) by State
| join (_detailed_data) on State
| extend EventPercentage = Events*100.0 / TotalStateEvents
| project State, EventType, EventPercentage, Events
| top 10 by EventPercentage
```

|State|Type d’événement|EventPercentage|Événements|
|---|---|---|---|
|EAUX HAWAII|Waterspout|100|2|
|LAKE ONTARIO|Vent Thunderstorm marin|100|8|
|GOLFE DE L’ALASKA|Waterspout|100|4|
|OUEST DU NORD|Vent Thunderstorm marin|95.2127659574468|179|
|LAKE ERIE|Vent Thunderstorm marin|92.5925925925926|25|
|E PACIFIQUE|Waterspout|90|9|
|LAKE MICHIGAN|Vent Thunderstorm marin|85.1648351648352|155|
|LAKE HURON|Vent Thunderstorm marin|79.3650793650794|50|
|GOLFE DU MEXIQUE|Vent Thunderstorm marin|71.7504332755633|414|
|Hawaï|Haute navigation|70.0218818380744|320|


L’exemple suivant génère un ensemble de nombres aléatoires et calcule : 
* nombre de valeurs distinctes dans le jeu (DCount)
* les trois premières valeurs du jeu 
* somme de toutes ces valeurs dans l’ensemble 
 
Cette opération peut être effectuée à l’aide de [traitements](batches.md) et matérialiser :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let randomSet = 
    materialize(
        range x from 1 to 3000000 step 1
        | project value = rand(10000000));
randomSet | summarize Dcount=dcount(value);
randomSet | top 3 by value;
randomSet | summarize Sum=sum(value)
```

Jeu de résultats 1 :  

|DCount|
|---|
|2578351|

Jeu de résultats 2 : 

|value|
|---|
|9999998|
|9999998|
|9999997|

Jeu de résultats 3 : 

|SUM|
|---|
|15002960543563|
