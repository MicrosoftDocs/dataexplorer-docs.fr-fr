---
title: bin ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit bin () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bb6c7c51e295f9af9d6e43a5de5936dfea13f5b6
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201669"
---
# <a name="bin"></a>bin()

Arrondit les valeurs à l’entier inférieur multiple d’une taille bin donnée. 

Utilisé fréquemment en association avec [`summarize by ...`](./summarizeoperator.md) .
Si vous avez un ensemble de valeurs dispersées, elles seront regroupées dans un plus petit ensemble de valeurs spécifiques.

Les valeurs NULL, une taille de compartiment null ou une taille de compartiment négative génèrent la valeur null. 

Alias pour `floor()` fonctionner.

## <a name="syntax"></a>Syntaxe

`bin(`*valeur* `,` *roundTo*`)`

## <a name="arguments"></a>Arguments

* *valeur*: nombre, date ou intervalle de dates. 
* *roundTo*: « taille de l’emplacement ». Nombre ou TimeSpan qui divise la *valeur*. 

## <a name="returns"></a>Retours

Multiple le plus proche de *roundTo*, inférieur à *value*.  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

## <a name="examples"></a>Exemples

Expression | Résultats
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


L’expression suivante calcule un histogramme de durées, avec une taille de compartiment de 1 seconde :

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```
