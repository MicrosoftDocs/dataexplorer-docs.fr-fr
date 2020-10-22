---
title: opérateur de conservation de projet-Azure Explorateur de données
description: Cet article décrit l’opérateur Project-Keep dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/21/2020
ms.openlocfilehash: 2efc06ad701cc734ffad0bec7d89e3d3ef1d390d
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356716"
---
# <a name="project-keep-operator"></a>projet-Keep, opérateur

Sélectionnez les colonnes de l’entrée à conserver dans la sortie.

```kusto
T | project-keep price, quantity, zz*
```

L’ordre des colonnes dans le résultat est déterminé par leur ordre d’origine dans la table. Seules les colonnes spécifiées en tant qu’arguments sont conservées. Les autres colonnes sont exclues du résultat. Voir aussi [`project`](projectoperator.md) .

## <a name="syntax"></a>Syntax

*T* `| project-keep` *ColumnNameOrPattern* [ `,` ...]

## <a name="arguments"></a>Arguments

* *T*: table d’entrée
* *ColumnNameOrPattern :* Nom du modèle générique de colonne ou de colonne à conserver dans la sortie.

## <a name="returns"></a>Retours

Table avec des colonnes nommées en tant qu’arguments. Contient le même nombre de lignes que la table d’entrée.

> [!TIP]
>* Pour renommer des colonnes, utilisez [`project-rename`](projectrenameoperator.md) .
>* Pour réorganiser les colonnes, utilisez [`project-reorder`](projectreorderoperator.md) .
>* Vous pouvez `project-keep` toutes les colonnes présentes dans la table d’origine ou qui ont été calculées dans le cadre de la requête.

## <a name="example"></a>Exemple

La table d’entrée `T` comporte trois colonnes de type `long` : `A`, `B` et `C`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A1:long, A2:long, B:long) [1, 2, 3]
| project-keep A*    // Keeps only columns A1 and A2 in the output
```

|A1|A2|
|---|---|
|1|2|

## <a name="see-also"></a>Voir aussi

Pour choisir les colonnes de l’entrée à exclure de la sortie, utilisez [projet-absent](projectawayoperator.md).
