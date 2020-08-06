---
title: opérateur de réorganisation de projet-Azure Explorateur de données
description: Cet article décrit l’opérateur de réorganisation de projet dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 98887c8044be6ea1b429c51953c6f3f9a899d090
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802959"
---
# <a name="project-reorder-operator"></a>project-reorder, opérateur

Réorganise les colonnes dans la sortie de résultat.

```kusto
T | project-reorder Col2, Col1, Col* asc
```

## <a name="syntax"></a>Syntaxe

*T* `| project-reorder` *ColumnNameOrPattern* [ `asc` | `desc` ] [ `,` ...]

## <a name="arguments"></a>Arguments

* *T*: table d’entrée.
* *ColumnNameOrPattern :* Nom du modèle de caractère générique de colonne ou de colonne ajouté à la sortie.
* Pour les modèles de caractères génériques : spécifier `asc` ou `desc` ordonner les colonnes à l’aide de leurs noms dans l’ordre croissant ou décroissant. Si `asc` ou `desc` ne sont pas spécifiés, l’ordre est déterminé par les colonnes correspondantes telles qu’elles apparaissent dans la table source.

> [!NOTE]
> * Dans une correspondance *ColumnNameOrPattern* ambiguë, la colonne apparaît à la première position correspondant au modèle.
> * La spécification de colonnes pour `project-reorder` est facultative. Les colonnes qui ne sont pas spécifiées explicitement apparaissent en tant que dernières colonnes de la table de sortie.
> * Utilisez [`project-away`](projectawayoperator.md) pour supprimer des colonnes.
> * Utilisez [`project-rename`](projectrenameoperator.md) pour renommer des colonnes.


## <a name="returns"></a>Retours

Table qui contient des colonnes dans l’ordre spécifié par les arguments de l’opérateur. `project-reorder`ne renomme pas ou ne supprime pas les colonnes de la table. par conséquent, toutes les colonnes qui existaient dans la table source apparaissent dans la table de résultats.

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
