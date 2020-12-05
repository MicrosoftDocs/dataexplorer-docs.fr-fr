---
title: Opérateurs in et notin - Azure Data Explorer
description: Cet article décrit les opérateurs in et notin dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.localizationpriority: high
ms.openlocfilehash: ffb24abe744bfbe3f7f95336edf0263becfa7ec9
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513248"
---
# <a name="in-and-in-operators"></a>in et !in, opérateurs

Filtre un jeu d’enregistrements en fonction de l’ensemble de valeurs fourni.

```kusto
Table1 | where col in ('value1', 'value2')
```

> [!NOTE]
> * Lorsque vous ajoutez « ~ » à l’opérateur, la recherche de valeurs devient insensible à la casse : `x in~ (expression)` ou `x !in~ (expression)`.
> * Dans les expressions tabulaires, la première colonne du jeu de résultats est sélectionnée.
> * La liste d’expressions peut produire jusqu’à `1,000,000` valeurs.
> * Les tableaux imbriqués sont aplatis en une seule liste de valeurs. Par exemple, `x in (dynamic([1,[2,3]]))` devient `x in (1,2,3)`.
 
## <a name="syntax"></a>Syntaxe

### <a name="case-sensitive-syntax"></a>Syntaxe respectant la casse

*T* `|` `where` *col* `in` `(`*liste d’expressions scalaires*`)`   
*T* `|` `where` *col* `in` `(`*expression tabulaire*`)`   
 
*T* `|` `where` *col* `!in` `(`*liste d’expressions scalaires*`)`  
*T* `|` `where` *col* `!in` `(`*expression tabulaire*`)`   

### <a name="case-insensitive-syntax"></a>Syntaxe non sensible à la casse

*T* `|` `where` *col* `in~` `(`*liste d’expressions scalaires*`)`   
*T* `|` `where` *col* `in~` `(`*expression tabulaire*`)`   
 
*T* `|` `where` *col* `!in~` `(`*liste d’expressions scalaires*`)`  
*T* `|` `where` *col* `!in~` `(`*expression tabulaire*`)`   

## <a name="arguments"></a>Arguments

* *T* : entrée tabulaire dont les enregistrements doivent être filtrés.
* *col* : colonne à filtrer.
* *liste d’expressions* : liste d’expressions tabulaires, scalaires ou littérales, séparées par des virgules.
* *expression tabulaire* : expression tabulaire qui possède un ensemble de valeurs. Si l’expression contient plusieurs colonnes, la première colonne est utilisée.

## <a name="returns"></a>Retours

Lignes dans *T* dont le prédicat est défini sur `true`.

## <a name="examples"></a>Exemples  

### <a name="use-in-operator"></a>Utiliser l’opérateur in

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Nombre|
|---|
|4775|  

### <a name="use-in-operator"></a>Utiliser l’opérateur in~  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Nombre|
|---|
|4775|  

### <a name="use-in-operator"></a>Utiliser l’opérateur !in

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Nombre|
|---|
|54291|  


### <a name="use-dynamic-array"></a>Utiliser un tableau dynamique

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let states = dynamic(['FLORIDA', 'ATLANTIC SOUTH', 'GEORGIA']);
StormEvents 
| where State in (states)
| count
```

|Nombre|
|---|
|3218|

### <a name="subquery"></a>Sous-requête

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Using subquery
let Top_5_States = 
StormEvents
| summarize count() by State
| top 5 by count_; 
StormEvents 
| where State in (Top_5_States) 
| count
```

La même requête peut être écrite comme suit :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Inline subquery 
StormEvents 
| where State in (
    ( StormEvents
    | summarize count() by State
    | top 5 by count_ )
) 
| count
```

|Nombre|
|---|
|14242|  

### <a name="top-with-other-example"></a>Top avec un autre exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| État     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| WISCONSIN | 31                   |
| TEXAS     | 55                   |
| FLORIDA   | 85 %                   |
| GEORGIA   | 106                  |
| Autres     | 415                  |

### <a name="use-a-static-list-returned-by-a-function"></a>Utiliser une liste statique retournée par une fonction

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Nombre|
|---|
|4775|  

Définition de fonction.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.show function InterestingStates
```

|Nom|Paramètres|Corps|Dossier|DocString|
|---|---|---|---|---|
|InterestingStates|()|{ dynamic(["WASHINGTON", "FLORIDA", "GEORGIA", "NEW YORK"]) }
