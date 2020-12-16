---
title: bin() – Azure Data Explorer | Microsoft Docs
description: Cet article décrit la fonction bin() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 2a49adf8befcd7c20bd4ec1180ccf937c7bfcfff
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388985"
---
# <a name="bin"></a>bin()

Arrondit les valeurs à l’entier inférieur multiple d’une taille bin donnée. 

Fonction fréquemment utilisée en combinaison avec [`summarize by ...`](./summarizeoperator.md).
Si vous avez un ensemble de valeurs dispersées, elles seront regroupées dans un plus petit ensemble de valeurs spécifiques.

Des valeurs null, une taille de compartiment null ou une taille de compartiment négative génèrent le résultat null. 

Alias de la fonction `floor()`.

## <a name="syntax"></a>Syntaxe

`bin(`*value*`,`*roundTo*`)`

## <a name="arguments"></a>Arguments

* *value* : nombre, date ou [timespan](scalar-data-types/timespan.md). 
* *roundTo* : « Taille de compartiment ». Nombre ou timespan qui divise *value*. 

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
