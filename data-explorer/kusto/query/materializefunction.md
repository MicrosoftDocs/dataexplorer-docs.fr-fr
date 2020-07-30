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
ms.openlocfilehash: 0e08857e01ffa3da1bcb23d16d1df4908336f76f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346862"
---
# <a name="materialize"></a>materialize()

Permet de mettre en cache le résultat d’une sous-requête pendant l’exécution de la requête d’une manière que d’autres sous-requêtes peuvent référencer le résultat partiel.
 
## <a name="syntax"></a>Syntaxe

`materialize(`*expression*`)`

## <a name="arguments"></a>Arguments

* *expression*: expression tabulaire à évaluer et à mettre en cache lors de l’exécution de la requête.

> [!NOTE]
> La matérialisation a une limite de taille de cache de **5 Go**. Cette limite est définie par nœud de cluster et est mutuelle pour toutes les requêtes qui s’exécutent simultanément. Si une requête utilise `materialize()` et que le cache ne peut pas contenir plus de données, la requête s’interrompt avec une erreur.

>[!TIP]
>
>* Transmettent tous les opérateurs possibles qui réduisent le jeu de données matérialisées et gardent la sémantique de la requête. Par exemple, utilisez des filtres, ou projetez uniquement les colonnes requises.
>* Utilisez matérialisation avec JOIN ou Union lorsque leurs opérandes ont des sous-requêtes réciproques qui peuvent être exécutées une seule fois. Par exemple, jointures/branches d’Union. Consultez [exemple d’utilisation de l’opérateur de jointure](#examples-of-query-performance-improvement).
>* Matérialiser ne peut être utilisé que dans les instructions Let si vous attribuez un nom au résultat mis en cache. Voir un [exemple d’utilisation des instructions Let](#examples-of-using-materialize)).

## <a name="examples-of-query-performance-improvement"></a>Exemples d’amélioration des performances des requêtes

L’exemple suivant montre comment `materialize()` peut être utilisé pour améliorer les performances de la requête.
L’expression `_detailed_data` est définie à l’aide `materialize()` de la fonction et, par conséquent, est calculée une seule fois.

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

|État|Type d’événement|EventPercentage|Événements|
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
* nombre de valeurs distinctes dans le jeu ( `Dcount` )
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

## <a name="examples-of-using-materialize"></a>Exemples d’utilisation de matérial ()

> [!TIP]
> Matérialisez votre colonne au moment de l’ingestion si la plupart de vos requêtes extraient des champs d’objets dynamiques entre des millions de lignes.

Pour utiliser l' `let` instruction avec une valeur que vous utilisez plusieurs fois, utilisez la [fonction matérialiser ()](./materializefunction.md). Essayez d’envoyer tous les opérateurs possibles qui réduiront le jeu de données matérialisées tout en gardant la sémantique de la requête. Par exemple, utilisez des filtres, ou projetez uniquement les colonnes requises.

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
```

Le filtre sur `Text` est mutuel et peut faire l’objet d’un push vers l’expression de matérialisation.
La requête a uniquement besoin des colonnes,, `Timestamp` `Text` `Resource1` et `Resource2` . Projetez ces colonnes à l’intérieur de l’expression matérialisée.
    
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
```
    
Si les filtres ne sont pas identiques, comme dans la requête suivante :  

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
 ```

Lorsque le filtre combiné réduit radicalement le résultat matérialisé, combinez les deux filtres sur le résultat matérialisé par une `or` expression logique comme dans la requête ci-dessous. Toutefois, conservez les filtres dans chaque tronçon d’Union pour préserver la sémantique de la requête.
     
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
```
    
