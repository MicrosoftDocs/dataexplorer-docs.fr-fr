---
title: bin() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit bin() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3fb827c71fa63fde031a91bc9aec7f0ed108fd5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517423"
---
# <a name="bin"></a>bin()

Arrondit les valeurs à l’entier inférieur multiple d’une taille bin donnée. 

Utilisé fréquemment en [`summarize by ...`](./summarizeoperator.md)combinaison avec .
Si vous avez un ensemble de valeurs dispersées, elles seront regroupées dans un plus petit ensemble de valeurs spécifiques.

Les valeurs nulles, la taille d’un bac nul ou la taille négative d’un bac se traduiront par l’annulation. 

Alias `floor()` pour fonctionner.

**Syntaxe**

`bin(`*ronde de*`,`*valeurTo*`)`

**Arguments**

* *valeur*: Un nombre, une date ou une durée. 
* *roundTo*: La "taille du bac". Nombre, date ou intervalle de temps qui divise *value*. 

**Retourne**

Multiple le plus proche de *roundTo*, inférieur à *value*.  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

**Exemples**

Expression | Résultats
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


L’expression suivante calcule un histogramme de durées, avec une taille de compartiment de 1 seconde :

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```