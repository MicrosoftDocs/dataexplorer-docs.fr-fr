---
title: set_has_element() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit set_has_element() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 256e01646c6ecd39a8a589299acd6620008ece28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507767"
---
# <a name="set_has_element"></a>set_has_element()

Détermine si l’ensemble spécifié contient l’élément spécifié.

**Syntaxe**

`set_has_element(`*tableau*,*valeur*`)`

**Arguments**

* *tableau*: Tableau d’entrée à rechercher.
* *valeur*: Valeur à rechercher. La valeur doit `long`être `integer` `double`de `datetime` `timespan`type `decimal` `string`, `guid`, , , , , , ou .

**Retourne**

Vrai ou faux selon que la valeur existe dans le tableau.

**Exemple**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|Résultats|
|---|
|1|

**Voir aussi**

Si vous êtes également intéressé par le poste auquel la valeur existe dans le tableau, vous pouvez utiliser [array_index_of (arr, valeur)](arrayindexoffunction.md). Les deux fonctions sont les mêmes, sur le plan de la performance.