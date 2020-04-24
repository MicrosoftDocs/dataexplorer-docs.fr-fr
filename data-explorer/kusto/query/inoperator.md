---
title: opérateurs in et notin - Azure Data Explorer ( Microsoft Docs
description: Cet article décrit dans et notin opérateurs dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: bd247de2bd211ae7be3da449e940899d2e8bb475
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513802"
---
# <a name="in-and-in-operators"></a>in et !in, opérateurs

Filtre un ensemble d’enregistrements basé sur l’ensemble de valeurs fourni.

```kusto
Table1 | where col in ('value1', 'value2')
```

**Syntaxe**

*Syntaxe sensible au cas :*

*Liste T* `|` `where` *col* `in` `(` *d’expressions scalaires*`)`   
*Expression* `|` `where` *tabulaire* de *col* `in` `(`T`)`   
 
*Liste T* `|` `where` *col* `!in` `(` *d’expressions scalaires*`)`  
*Expression* `|` `where` *tabulaire* de *col* `!in` `(`T`)`   

*Syntaxe insensible au cas :*

*Liste T* `|` `where` *col* `in~` `(` *d’expressions scalaires*`)`   
*Expression* `|` `where` *tabulaire* de *col* `in~` `(`T`)`   
 
*Liste T* `|` `where` *col* `!in~` `(` *d’expressions scalaires*`)`  
*Expression* `|` `where` *tabulaire* de *col* `!in~` `(`T`)`   

**Arguments**

* *T* - L’entrée tabulaire dont les enregistrements doivent être filtrés.
* *col* - la colonne pour filtrer.
* *liste d’expressions* - une liste séparée de virgule d’expressions tabulaires, scalaires ou littérales  
* *expression tabulaire* - une expression tabulaire qui a un ensemble de valeurs (dans un cas expression a plusieurs colonnes, la première colonne est utilisée)

**Retourne**

Lignes en *T* pour lesquelles le prédicat est`true`

**Remarques**

* La liste d’expression `1,000,000` peut produire jusqu’à des valeurs    
* Les tableaux imbriqués sont aplatis en `x in (dynamic([1,[2,3]]))` une seule liste de valeurs, par exemple`x in (1,2,3)` 
* En cas d’expressions tabulaires, la première colonne de l’ensemble de résultats est sélectionnée   
* L’ajout de « « » à l’opérateur rend insensible le cas de recherche des valeurs : `x in~ (expression)` ou `x !in~ (expression)`.

**Exemples :**  

**Une simple utilisation de l’opérateur 'in' :**  

```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|4775|  


**Une simple utilisation de l’opérateur 'in':**  

```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Count|
|---|
|4775|  

**Une utilisation simple de '!in' opérateur:**  

```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|54291|  


**Utilisation d’un tableau dynamique :**
```kusto
let states = dynamic(['FLORIDA', 'ATLANTIC SOUTH', 'GEORGIA']);
StormEvents 
| where State in (states)
| count
```

|Count|
|---|
|3218|


**Un exemple de sous-fertilité :**  

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

La même requête peut être écrite comme:

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

|Count|
|---|
|14242|  

**Haut avec un autre exemple:**  

```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| State     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| Wisconsin | 31                   |
| TEXAS     | 55                   |
| Floride   | 85 %                   |
| Géorgie   | 106                  |
| Autres     | 415                  |

**Utilisation d’une liste statique retournée par une fonction :**  

```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Count|
|---|
|4775|  


Voici la définition de la fonction:  

```kusto
.show function InterestingStates
```

|Nom|Paramètres|body|Dossier|DocString (en)|
|---|---|---|---|---|
|Intéressant États|()|dynamique (["WASHINGTON", "FLORIDA", "GEORGIA", "NEW YORK"))
