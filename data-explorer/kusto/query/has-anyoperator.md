---
title: has_any opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit has_any opérateur dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 42a181f7c75ef36119f7f2915fd5327d4a656eb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514312"
---
# <a name="has_any-operator"></a>has_any, opérateur

`has_any`filtres d’opérateur basés sur l’ensemble de valeurs fournis.

```kusto
Table1 | where col has_any ('value1', 'value2')
```

**Syntaxe**

*Liste T* `|` `where` *col* `has_any` `(` *d’expressions scalaires*`)`   
*Expression* `|` `where` *tabulaire* de *col* `has_any` `(`T`)`   
 
**Arguments**

* *T* - Entrée Tabulaire dont les enregistrements doivent être filtrés.
* *col* - Colonne à filtrer.
* *liste d’expressions* - Liste séparée de la comma des expressions tabulaires, scalaires ou littérales  
* *expression tabulaire* - expression tabulaire qui a un ensemble de valeurs (si l’expression a plusieurs colonnes, la première colonne est utilisée)

**Retourne**

Lignes en *T* pour lesquelles le prédicat est`true`

**Remarques**

* La liste d’expression `10,000` peut produire jusqu’à des valeurs.    
* Pour les expressions tabulaires, la première colonne de l’ensemble de résultats est sélectionnée.   

**Exemples :**  

**Une simple `has_any` utilisation de l’opérateur :**  

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
|NOUVEAU MAILLOT|1044|
|CAROLINE DU SUD|915|
|DAKOTA DU NORD|905|
|NOUVEAU-MEXIQUE|527|
|NEW HAMPSHIRE|394|


**Utilisation d’un tableau dynamique :**

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
|ATLANTIQUE SUD|193|
|ATLANTIQUE NORD|188|