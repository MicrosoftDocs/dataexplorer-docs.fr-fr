---
title: set_has_element ()-Azure Explorateur de données
description: Cet article décrit set_has_element () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 314244c58eca6082b9042b263e6b3e6faeb69840
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351197"
---
# <a name="set_has_element"></a>set_has_element()

Détermine si le jeu spécifié contient l’élément spécifié.

## <a name="syntax"></a>Syntaxe

`set_has_element(`*tableau*,*valeur*`)`

## <a name="arguments"></a>Arguments

* *tableau*: tableau d’entrée à rechercher.
* *valeur*: valeur à rechercher. La valeur doit être de type `long` ,,, `integer` `double` `datetime` , `timespan` , `decimal` , `string` ou `guid` .

## <a name="returns"></a>Retourne

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

**Voir aussi**

Utilisez [`array_index_of(arr, value)`](arrayindexoffunction.md) pour rechercher la position à laquelle la valeur existe dans le tableau. Les deux fonctions sont tout aussi performantes.
