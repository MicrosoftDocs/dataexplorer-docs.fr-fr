---
title: Opérateur project – Azure Data Explorer | Microsoft Docs
description: Cet article décrit l’opérateur project dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: f94003573cab076d8fa83537cb7868e21b9b084c
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513265"
---
# <a name="project-operator"></a>opérateur project

Sélectionnez les colonnes à inclure, renommer ou supprimer, puis insérez les nouvelles colonnes calculées. 

L’ordre des colonnes dans le résultat est déterminé par l’ordre des arguments. Seules les colonnes spécifiées dans les arguments sont incluses dans le résultat. Toutes les autres colonnes de l’entrée sont supprimées.  (Voir aussi `extend`.)

```kusto
T | project cost=price*quantity, price
```

## <a name="syntax"></a>Syntaxe

*T* `| project` *ColumnName* [`=` *Expression*] [`,` ...]
  
or
  
*T* `| project` [*ColumnName* | `(`*ColumnName*[`,`]`)` `=`] *Expression* [`,` ...]

## <a name="arguments"></a>Arguments

* *T* : table d’entrée.
* *ColumnName:* nom facultatif d’une colonne à afficher dans la sortie. En l’absence d’*Expression*, *ColumnName* est obligatoire et une colonne de ce nom doit apparaître dans l’entrée. En cas d’omission, le nom est généré automatiquement. Si l’*Expression* retourne plusieurs colonnes, une liste de noms de colonnes peut être spécifiée entre parenthèses. Dans ce cas, les colonnes de sortie de l’*expression* reçoivent les noms spécifiés, et toutes les éventuelles colonnes de sortie restantes sont supprimées. Si une liste de noms de colonnes n’est pas spécifiée, toutes les colonnes de sortie de l’*Expression* avec des noms générés sont ajoutées à la sortie.
* *Expression :* expression scalaire facultative faisant référence aux colonnes d’entrée. Si *ColumnName* n’est pas omis, l’*Expression* est obligatoire.

    Il est possible de retourner une nouvelle colonne calculée portant le même nom qu’une colonne figurant dans l’entrée.

## <a name="returns"></a>Retours

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