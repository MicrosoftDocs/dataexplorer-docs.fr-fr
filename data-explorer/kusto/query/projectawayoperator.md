---
title: opérateur Project-Away-Azure Explorateur de données
description: Cet article décrit l’opérateur Project-Away dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2938019237c882891af8ff86f4d33de3605a9063
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356518"
---
# <a name="project-away-operator"></a>opérateur project-away

Sélectionnez les colonnes de l’entrée à exclure de la sortie.

```kusto
T | project-away price, quantity, zz*
```

L’ordre des colonnes dans le résultat est déterminé par leur ordre d’origine dans la table. Seules les colonnes qui ont été spécifiées en tant qu’arguments sont supprimées. Les autres colonnes sont incluses dans le résultat. (Voir aussi `project`.)

## <a name="syntax"></a>Syntax

*T* `| project-away` *ColumnNameOrPattern* [ `,` ...]

## <a name="arguments"></a>Arguments

* *T*: table d’entrée
* *ColumnNameOrPattern :* Nom du modèle générique de colonne ou de colonne à supprimer de la sortie.

## <a name="returns"></a>Retours

Table dont les colonnes ne sont pas nommées en tant qu’arguments. Contient le même nombre de lignes que la table d’entrée.

> [!TIP]
>
> * Pour renommer des colonnes, utilisez [`project-rename`](projectrenameoperator.md) .
> * Pour réorganiser les colonnes, utilisez [`project-reorder`](projectreorderoperator.md) .
> * Vous pouvez `project-away` toutes les colonnes présentes dans la table d’origine ou qui ont été calculées dans le cadre de la requête.

## <a name="examples"></a>Exemples

La table d’entrée `T` comporte trois colonnes de type `long` : `A`, `B` et `C`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|Un|B|
|---|---|
|1|2|

Suppression des colonnes à partir de « a ».

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a2='a2', b = 'b', a3='a3', a1='a1'
| project-away a*
```

|b|
|---|
|b|

## <a name="see-also"></a>Voir aussi

Pour choisir les colonnes de l’entrée à conserver dans la sortie, utilisez [Project-Keep](project-keep-operator.md).
