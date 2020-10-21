---
title: array_index_of ()-Azure Explorateur de données
description: Cet article décrit array_index_of () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: b06ae72c872575f7fc45741c4b023f82440815db
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246884"
---
# <a name="array_index_of"></a>array_index_of()

Recherche l’élément spécifié dans le tableau et retourne sa position.

## <a name="syntax"></a>Syntaxe

`array_index_of(`*tableau*,*valeur*`)`

## <a name="arguments"></a>Arguments

* *tableau*: tableau d’entrée à rechercher.
* *valeur*: valeur à rechercher. La valeur doit être de type long, Integer, double, DateTime, TimeSpan, Decimal, String ou GUID.

## <a name="returns"></a>Retours

Position d’index de base zéro de la recherche.
Retourne-1 si la valeur est introuvable dans le tableau.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Résultat|
|---|
|3|

## <a name="see-also"></a>Voir aussi

Si vous souhaitez uniquement vérifier si une valeur existe dans un tableau, mais que vous n’êtes pas intéressé par sa position, vous pouvez utiliser [set_has_element ( `arr` , `value` )](sethaselementfunction.md). Cette fonction permet d’améliorer la lisibilité de votre requête. Les deux fonctions ont les mêmes performances.
