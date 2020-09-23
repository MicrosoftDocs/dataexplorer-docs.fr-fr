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
ms.openlocfilehash: b62e5032d6f2ccedc2883b6cbccaf7be69e1cebf
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103517"
---
# <a name="set_has_element"></a>set_has_element()

Détermine si le jeu spécifié contient l’élément spécifié.

## <a name="syntax"></a>Syntaxe

`set_has_element(`*tableau*,*valeur*`)`

## <a name="arguments"></a>Arguments

* *tableau*: tableau d’entrée à rechercher.
* *valeur*: valeur à rechercher. La valeur doit être de type `long` ,,, `integer` `double` `datetime` , `timespan` , `decimal` , `string` ou `guid` .

## <a name="returns"></a>retourne :

True ou false selon que la valeur existe dans le tableau.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|Résultats|
|---|
|1|

## <a name="see-also"></a>Voir aussi

Utilisez [`array_index_of(arr, value)`](arrayindexoffunction.md) pour rechercher la position à laquelle la valeur existe dans le tableau. Les deux fonctions sont tout aussi performantes.
