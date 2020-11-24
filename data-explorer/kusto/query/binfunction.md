---
title: bin ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit bin () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6fc2e55b43e7c7c2dc2bb537730f8f627e3e4a66
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513112"
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
