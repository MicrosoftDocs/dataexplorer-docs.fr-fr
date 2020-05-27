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
ms.openlocfilehash: 8e96b97754600d308526a4cb059907521fda0521
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83862926"
---
# <a name="set_has_element"></a>set_has_element()

Détermine si le jeu spécifié contient l’élément spécifié.

**Syntaxe**

`set_has_element(`*tableau*,*valeur*`)`

**Arguments**

* *tableau*: tableau d’entrée à rechercher.
* *valeur*: valeur à rechercher. La valeur doit être de type `long` ,,, `integer` `double` `datetime` , `timespan` , `decimal` , `string` ou `guid` .

**Retourne**

True ou false selon que la valeur existe dans le tableau.

**Exemple**

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
