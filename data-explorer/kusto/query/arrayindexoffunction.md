---
title: array_index_of ()-Azure Explorateur de données
description: Cet article décrit array_index_of () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: 99044d8762a1c7c7e86fb2633a8226ef48d66b55
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225630"
---
# <a name="array_index_of"></a>array_index_of()

Recherche l’élément spécifié dans le tableau et retourne sa position.

**Syntaxe**

`array_index_of(`*tableau*,*valeur*`)`

**Arguments**

* *tableau*: tableau d’entrée à rechercher.
* *valeur*: valeur à rechercher. La valeur doit être de type long, Integer, double, DateTime, TimeSpan, Decimal, String ou GUID.

**Retourne**

Position d’index de base zéro de la recherche.
Retourne-1 si la valeur est introuvable dans le tableau.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Résultats|
|---|
|3|

**Voir aussi**

Si vous souhaitez uniquement vérifier si une valeur existe dans un tableau, mais que vous n’êtes pas intéressé par sa position, vous pouvez utiliser [set_has_element ( `arr` , `value` )](sethaselementfunction.md). Cette fonction permet d’améliorer la lisibilité de votre requête. Les deux fonctions ont les mêmes performances.
