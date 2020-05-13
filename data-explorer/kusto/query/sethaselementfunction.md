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
ms.openlocfilehash: 9cf2ec4371f4aeef8a68cb65fb2b946b9c393054
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372370"
---
# <a name="set_has_element"></a>set_has_element()

Détermine si le jeu spécifié contient l’élément spécifié.

**Syntaxe**

`set_has_element(`*tableau*,*valeur*`)`

**Arguments**

* *tableau*: tableau d’entrée à rechercher.
* *valeur*: valeur à rechercher. La valeur doit être de type `long` ,,, `integer` `double` `datetime` , `timespan` , `decimal` , `string` ou `guid` .

**Retourne**

True ou false selon que la valeur existe ou non dans le tableau.

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|Résultats|
|---|
|1|

**Voir aussi**

Si vous êtes également intéressé par la position à laquelle la valeur existe dans le tableau, vous pouvez utiliser [array_index_of (arr, value)](arrayindexoffunction.md). Les deux fonctions sont les mêmes, en termes de performances.
