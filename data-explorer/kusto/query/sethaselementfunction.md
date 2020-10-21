---
title: set_has_element ()-Azure Explorateur de données
description: Cet article décrit set_has_element () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: b32fd7361d98c47c8e93814db6b794a762fad1cf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247198"
---
# <a name="set_has_element"></a>set_has_element()

Détermine si le jeu spécifié contient l’élément spécifié.

## <a name="syntax"></a>Syntaxe

`set_has_element(`*tableau*,*valeur*`)`

## <a name="arguments"></a>Arguments

* *tableau*: tableau d’entrée à rechercher.
* *valeur*: valeur à rechercher. La valeur doit être de type `long` ,,, `integer` `double` `datetime` , `timespan` , `decimal` , `string` ou `guid` .

## <a name="returns"></a>Retours

True ou false selon que la valeur existe dans le tableau.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|Résultat|
|---|
|1|

## <a name="see-also"></a>Voir aussi

Utilisez [`array_index_of(arr, value)`](arrayindexoffunction.md) pour rechercher la position à laquelle la valeur existe dans le tableau. Les deux fonctions sont tout aussi performantes.
