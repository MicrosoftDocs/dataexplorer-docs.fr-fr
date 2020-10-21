---
title: Opérateurs numériques-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les opérateurs numériques dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b79979abdc13d5cb6b7a71bde2301ed0169ae59b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249751"
---
# <a name="numerical-operators"></a>Opérateurs numériques

Les types `int` , `long` et `real` représentent des types numériques.
Les opérateurs suivants peuvent être utilisés entre les paires de ces types :

Opérateur       |Description                         |Exemple
---------------|------------------------------------|-----------------------
`+`            |Ajouter                                 |`3.14 + 3.14`, `ago(5m) + 5m`
`-`            |Soustraire                            |`0.23 - 0.22`,
`*`            |Multiplier                            |`1s * 5`, `2 * 2`
`/`            |Diviser                              |`10m / 1s`, `4 / 2`
`%`            |Modulo                              |`4 % 2`
`<`            |Less                                |`1 < 10`, `10sec < 1h`, `now() < datetime(2100-01-01)`
`>`            |Greater                             |`0.23 > 0.22`, `10min > 1sec`, `now() > ago(1d)`
`==`           |Est égal à                              |`1 == 1`
`!=`           |Différent de                          |`1 != 0`
`<=`           |Less or Equal                       |`4 <= 5`
`>=`           |Greater or Equal                    |`5 >= 4`
`in`           |Égal à l’un des éléments       |[Voir ici](inoperator.md)
`!in`          |N’est égal à aucun des éléments   |[Voir ici](inoperator.md)

**Commentaire concernant l’opérateur modulo**

Le modulo de deux nombres retourne toujours dans Kusto un « petit nombre non négatif ».
Ainsi, le modulo de deux nombres, *N*  %  *d*, est tel que : 0 &le; (*n*  %  *d*) &lt; ABS (*d*).

Par exemple, la requête suivante :

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

Produit le résultat suivant :

|plusPlus  | minusPlus  | plusMinus  | minusMinus|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |