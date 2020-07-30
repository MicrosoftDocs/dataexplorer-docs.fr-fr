---
title: opérateur Project-Away-Azure Explorateur de données
description: Cet article décrit l’opérateur Project-Away dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 40bc5eafee803123ea1d73e763c32b5210f741ca
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346046"
---
# <a name="project-away-operator"></a>opérateur project-away

Sélectionner les colonnes de l’entrée à exclure de la sortie

```kusto
T | project-away price, quantity, zz*
```

L’ordre des colonnes dans le résultat est déterminé par leur ordre d’origine dans la table. Seules les colonnes qui ont été spécifiées en tant qu’arguments sont supprimées. Les autres colonnes sont incluses dans le résultat.  (Voir aussi `project`.)

## <a name="syntax"></a>Syntaxe

*T* `| project-away` *ColumnNameOrPattern* [ `,` ...]

## <a name="arguments"></a>Arguments

* *T*: table d’entrée
* *ColumnNameOrPattern :* Nom du modèle générique de colonne ou de colonne à supprimer de la sortie.

## <a name="returns"></a>Retourne

Table dont les colonnes ne sont pas nommées en tant qu’arguments. Contient le même nombre de lignes que la table d’entrée.

**Conseils**

* Utilisez [`project-rename`](projectrenameoperator.md) si vous avez l’intention de renommer des colonnes.
* Utilisez [`project-reorder`](projectreorderoperator.md) si vous avez l’intention de réorganiser les colonnes.

* Vous pouvez `project-away` toutes les colonnes présentes dans la table d’origine ou qui ont été calculées dans le cadre de la requête.


## <a name="examples"></a>Exemples

La table d’entrée `T` comporte trois colonnes de type `long` : `A`, `B` et `C`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|A|B|
|---|---|
|1|2|

Suppression des colonnes à partir de « a ».

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

