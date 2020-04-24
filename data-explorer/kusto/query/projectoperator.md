---
title: Opérateur de projet - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur du projet dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de13c240180d00b82736a0dd35cb83c08639682f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510929"
---
# <a name="project-operator"></a>Opérateur de projet

Sélectionnez les colonnes à inclure, renommer ou supprimer, puis insérez les nouvelles colonnes calculées. 

L’ordre des colonnes dans le résultat est déterminé par l’ordre des arguments. Seules les colonnes spécifiées dans les arguments sont incluses dans le résultat. Toutes les autres colonnes dans l’entrée sont abandonnées.  (Voir aussi `extend`.)

```kusto
T | project cost=price*quantity, price
```

**Syntaxe**

*T* `| project` *ColumnName* [`=` `,` *Expression*] [ ...]
  
or
  
*T* `| project` [*ColumnName* | `(`*ColumnName*`,`[ ]`)` `=`] *Expression* [`,` ...]

**Arguments**

* *T*: La table d’entrée.
* *Nom de colonne:* Nom facultatif d’une colonne à apparaître dans la sortie. S’il n’y a pas *d’expression,* alors *ColumnName* est obligatoire et une colonne de ce nom doit apparaître dans l’entrée. S’il est omis, le nom sera automatiquement généré. Si *Expression* renvoie plus d’une colonne, une liste de noms de colonnes peut être spécifiée entre parenthèses. Dans ce cas, les colonnes de sortie *d’Expression*recevront les noms spécifiés, laissant tomber tout le reste des colonnes de sortie, s’il y en a. Si la liste des noms de colonnes n’est pas spécifiée, toutes les colonnes de sortie *d’Expression*avec des noms générés seront ajoutées à la sortie.
* *Expression :* expression scalaire facultative faisant référence aux colonnes d’entrée. Si *ColumnName n’est* pas omis, alors *Expression* est obligatoire.

    Il est possible de retourner une nouvelle colonne calculée portant le même nom qu’une colonne figurant dans l’entrée.

**Retourne**

Une table contenant les colonnes nommées en tant qu’arguments, et autant de lignes que la table d’entrée.

**Exemple**

L’exemple suivant présente plusieurs types de manipulations pouvant être effectuées à l’aide de l’opérateur `project` . La table d’entrée `T` comporte trois colonnes de type `int` : `A`, `B` et `C`. 

```kusto
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```

[series_stats](series-statsfunction.md) est un exemple d’une fonction qui renvoie plusieurs colonnes.