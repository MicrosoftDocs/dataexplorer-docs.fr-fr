---
title: opérateur de réorganisation de projet-Azure Explorateur de données
description: Cet article décrit l’opérateur de réorganisation de projet dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de8ff4b9256c8f964350bafd64eac15b028f53d4
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356484"
---
# <a name="project-reorder-operator"></a>project-reorder, opérateur

Réorganise les colonnes dans la sortie de résultat.

```kusto
T | project-reorder Col2, Col1, Col* asc
```

## <a name="syntax"></a>Syntax

*T* `| project-reorder` *ColumnNameOrPattern* [ `asc` | `desc` ] [ `,` ...]

## <a name="arguments"></a>Arguments

* *T*: table d’entrée.
* *ColumnNameOrPattern :* Nom du modèle de caractère générique de colonne ou de colonne ajouté à la sortie.
* Pour les modèles de caractères génériques : spécifier `asc` ou `desc` ordonner les colonnes à l’aide de leurs noms dans l’ordre croissant ou décroissant. Si `asc` ou `desc` ne sont pas spécifiés, l’ordre est déterminé par les colonnes correspondantes telles qu’elles apparaissent dans la table source.

> [!NOTE]
> * Dans une correspondance *ColumnNameOrPattern* ambiguë, la colonne apparaît à la première position correspondant au modèle.
> * La spécification de colonnes pour `project-reorder` est facultative. Les colonnes qui ne sont pas spécifiées explicitement apparaissent en tant que dernières colonnes de la table de sortie.
> * Pour supprimer des colonnes, utilisez [`project-away`](projectawayoperator.md) .
> * Pour choisir les colonnes à conserver, utilisez [`project-keep`](project-keep-operator.md) .
> * Pour renommer des colonnes, utilisez [`project-rename`](projectrenameoperator.md) .

## <a name="returns"></a>Retours

Table qui contient des colonnes dans l’ordre spécifié par les arguments de l’opérateur. `project-reorder` ne renomme pas ou ne supprime pas les colonnes de la table. par conséquent, toutes les colonnes qui existaient dans la table source apparaissent dans la table de résultats.

## <a name="examples"></a>Exemples

Réorganiser une table avec trois colonnes (a, b, c) pour que la deuxième colonne (b) s’affiche en premier.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|b|a|c|
|---|---|---|
|b|a|c|

Réorganiser les colonnes d’une table de sorte que les colonnes commençant par `a` s’affichent avant les autres colonnes.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|cellule|R2|formats|b|
|---|---|---|---|
|cellule|R2|formats|b|
