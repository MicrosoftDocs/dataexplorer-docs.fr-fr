---
title: opérateur has_any-Azure Explorateur de données
description: Cet article décrit has_any opérateur dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 012a6b0555778a30055ac9d7f4619c7b74d13988
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241622"
---
# <a name="has_any-operator"></a>has_any, opérateur

`has_any` filtres d’opérateur basés sur l’ensemble de valeurs fourni.

```kusto
Table1 | where col has_any ('value1', 'value2')
```

## <a name="syntax"></a>Syntaxe

*T* `|` `where` *col* `has_any` `(` *Liste de colonnes T d’expressions scalaires*`)`   
*T* `|` `where` *col* `has_any` `(` *Expression tabulaire* T col`)`   
 
## <a name="arguments"></a>Arguments

* Entrée *T* -tabulaire dont les enregistrements doivent être filtrés.
* *col* -colonne à filtrer.
* *liste d’expressions* : liste séparée par des virgules d’expressions tabulaires, scalaires ou littérales  
* *expression tabulaire* -expression tabulaire avec un ensemble de valeurs (si l’expression comporte plusieurs colonnes, la première colonne est utilisée)

## <a name="returns"></a>Retours

Lignes dans *T* pour lesquelles le prédicat est `true`

**Notes**

* La liste d’expressions peut produire jusqu’à `10,000` valeurs.    
* Pour les expressions tabulaires, la première colonne du jeu de résultats est sélectionnée.   

**Exemples :**  

**Utilisation simple de l' `has_any` opérateur :**  

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where State has_any ("CAROLINA", "DAKOTA", "NEW") 
| summarize count() by State
```

|State|count_|
|---|---|
|NEW YORK|1750|
|CAROLINE DU NORD|1721|
|DAKOTA DU SUD|1567|
|NEW JERSEY|1044|
|CAROLINE DU SUD|915|
|DAKOTA DU NORD|905|
|NOUVEAU MEXIQUE|527|
|NOUVEAU HAMPSHIRE|394|


**Utilisation d’un tableau dynamique :**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let states = dynamic(['south', 'north']);
StormEvents 
| where State has_any (states)
| summarize count() by State
```

|State|count_|
|---|---|
|CAROLINE DU NORD|1721|
|DAKOTA DU SUD|1567|
|CAROLINE DU SUD|915|
|DAKOTA DU NORD|905|
|SUD DE L’ATLANTIQUE|193|
|OUEST DU NORD|188|
