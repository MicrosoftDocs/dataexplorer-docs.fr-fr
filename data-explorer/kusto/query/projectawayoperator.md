---
title: opérateur de projet -l’opérateur d’azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de projet-away dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 38ec57e9659458ef34117e4a380c756310db218b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510946"
---
# <a name="project-away-operator"></a>opérateur project-away

Sélectionnez les colonnes de l’entrée à exclure de la sortie

```kusto
T | project-away price, quantity, zz*
```

L’ordre des colonnes dans le résultat est déterminé par leur ordre d’origine dans le tableau. Seules les colonnes qui ont été spécifiées comme arguments sont abandonnées. Les autres colonnes sont incluses dans le résultat.  (Voir aussi `project`.)

**Syntaxe**

*T* `| project-away` *ColumnNameOrPattern* [`,` ...]

**Arguments**

* *T*: La table d’entrée
* *ColonneNameOrPattern:* Le nom de la colonne ou de la colonne wildcard-modèle à supprimer de la sortie.

**Retourne**

Un tableau avec des colonnes qui n’ont pas été nommés comme des arguments. Contient le même nombre de lignes que la table d’entrée.

**Conseils**

* Utilisez [`project-rename`](projectrenameoperator.md) si votre intention est de renommer les colonnes.
* Utilisez [`project-reorder`](projectreorderoperator.md) si votre intention est de réorganiser les colonnes.

* Vous `project-away` pouvez toutes les colonnes qui sont présentes dans le tableau d’origine ou qui ont été calculés dans le cadre de la requête.


**Exemples**

La table d’entrée `T` comporte trois colonnes de type `long` : `A`, `B` et `C`.

```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|Un|B|
|---|---|
|1|2|

Suppression des colonnes en commençant par 'a'.

```kusto
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

