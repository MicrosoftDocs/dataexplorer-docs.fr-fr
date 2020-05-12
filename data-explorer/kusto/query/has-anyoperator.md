---
title: opérateur has_any-Azure Explorateur de données
description: Cet article décrit has_any opérateur dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 19329b8822a1e1d484c5f751f5fbc2f8eb6343ac
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226735"
---
# <a name="has_any-operator"></a>has_any, opérateur

`has_any`filtres d’opérateur basés sur l’ensemble de valeurs fourni.

```kusto
Table1 | where col has_any ('value1', 'value2')
```

**Syntaxe**

*T* `|` `where` *col* `has_any` `(` *Liste de colonnes T d’expressions scalaires*`)`   
*T* `|` `where` *col* `has_any` `(` *Expression tabulaire* T col`)`   
 
**Arguments**

* Entrée *T* -tabulaire dont les enregistrements doivent être filtrés.
* *col* -colonne à filtrer.
* *liste d’expressions* : liste séparée par des virgules d’expressions tabulaires, scalaires ou littérales  
* *expression tabulaire* -expression tabulaire avec un ensemble de valeurs (si l’expression comporte plusieurs colonnes, la première colonne est utilisée)

**Retourne**

Lignes dans *T* pour lesquelles le prédicat est`true`

**Remarques**

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

|État|count_|
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

|État|count_|
|---|---|
|CAROLINE DU NORD|1721|
|DAKOTA DU SUD|1567|
|CAROLINE DU SUD|915|
|DAKOTA DU NORD|905|
|SUD DE L’ATLANTIQUE|193|
|OUEST DU NORD|188|
