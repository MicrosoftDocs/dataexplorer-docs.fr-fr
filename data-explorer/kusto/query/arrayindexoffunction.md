---
title: array_index_of() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit array_index_of() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f68c9385c55cedb4491033d137af087dafd26aa0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518613"
---
# <a name="array_index_of"></a>array_index_of()

Recherche le tableau pour l’élément spécifié, et retourne sa position.

**Syntaxe**

`array_index_of(`*tableau*,*valeur*`)`

**Arguments**

* *tableau*: Tableau d’entrée à rechercher.
* *valeur*: Valeur à rechercher. La valeur doit être de type long, integer, double, datetime, timespan, décimale, ficelle ou guid.

**Retourne**

Position de l’indice zéro de la recherche.
Retourne -1 si la valeur n’est pas trouvée dans le tableau.

**Exemple**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Résultats|
|---|
|3|

**Voir aussi**

Si vous voulez seulement vérifier si une valeur existe dans un tableau, mais vous n’êtes pas intéressé par sa position, vous pouvez utiliser [set_has_element (arr, valeur)](sethaselementfunction.md) - cela permettra d’améliorer la lisibilité de votre requête, mais les performances-sages les deux fonctions sont les mêmes.