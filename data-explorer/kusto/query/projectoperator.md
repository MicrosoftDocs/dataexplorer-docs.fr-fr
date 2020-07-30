---
title: Opérateur de projet-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur Project dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7a7cbb563a10b1cd1bdd91f12b0ce9d7da1c0e7b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346029"
---
# <a name="project-operator"></a>opérateur project

Sélectionnez les colonnes à inclure, renommer ou supprimer, puis insérez les nouvelles colonnes calculées. 

L’ordre des colonnes dans le résultat est déterminé par l’ordre des arguments. Seules les colonnes spécifiées dans les arguments sont incluses dans le résultat. Toutes les autres colonnes de l’entrée sont supprimées.  (Voir aussi `extend`.)

```kusto
T | project cost=price*quantity, price
```

## <a name="syntax"></a>Syntaxe

*T* `| project` *ColumnName* [ `=` *expression*] [ `,` ...]
  
or
  
*T* `| project` [*ColumnName*  |  `(` *ColumnName*[ `,` ] `)` `=` ] *expression* [ `,` ...]

## <a name="arguments"></a>Arguments

* *T*: table d’entrée.
* *ColumnName :* Nom facultatif d’une colonne à afficher dans la sortie. S’il n’y a aucune *expression*, *ColumnName* est obligatoire et une colonne de ce nom doit apparaître dans l’entrée. En cas d’omission, le nom est généré automatiquement. Si l' *expression* retourne plusieurs colonnes, une liste de noms de colonnes peut être spécifiée entre parenthèses. Dans ce cas, les colonnes de sortie de l' *expression*reçoivent les noms spécifiés, en supprimant toutes les autres colonnes de sortie, le cas échéant. Si la liste des noms de colonnes n’est pas spécifiée, toutes les colonnes de sortie de l' *expression*avec des noms générés sont ajoutées à la sortie.
* *Expression :* expression scalaire facultative faisant référence aux colonnes d’entrée. Si *ColumnName* n’est pas omis, *expression* est obligatoire.

    Il est possible de retourner une nouvelle colonne calculée portant le même nom qu’une colonne figurant dans l’entrée.

## <a name="returns"></a>Retourne

Une table contenant les colonnes nommées en tant qu’arguments, et autant de lignes que la table d’entrée.

## <a name="example"></a>Exemple

L’exemple suivant présente plusieurs types de manipulations pouvant être effectuées à l’aide de l’opérateur `project` . La table d’entrée `T` comporte trois colonnes de type `int` : `A`, `B` et `C`. 

```kusto
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```

[series_stats](series-statsfunction.md) est un exemple de fonction qui retourne plusieurs colonnes.