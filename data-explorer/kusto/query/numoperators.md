---
title: Opérateurs numériques - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les opérateurs numériques dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6667005960152814be952d93fe932b4971d5322b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512017"
---
# <a name="numerical-operators"></a>Opérateurs numériques

Les `int`types `long`, `real` , et représentent les types numériques.
Les opérateurs suivants peuvent être utilisés entre les paires de ces types :

Opérateur       |Description                         |Exemple
---------------|------------------------------------|-----------------------
`+`            |Ajouter                                 |`3.14 + 3.14`, `ago(5m) + 5m`
`-`            |Soustraire                            |`0.23 - 0.22`,
`*`            |Multiplier                            |`1s * 5`, `2 * 2`
`/`            |Diviser                              |`10m / 1s`, `4 / 2`
`%`            |Modulo                              |`4 % 2`
`<`            |Inférieur à                                |`1 < 10`, `10sec < 1h`, `now() < datetime(2100-01-01)`
`>`            |Supérieur à                             |`0.23 > 0.22`, `10min > 1sec`, `now() > ago(1d)`
`==`           |Égal à                              |`1 == 1`
`!=`           |Non égal à                          |`1 != 0`
`<=`           |Inférieur ou égal à                       |`4 <= 5`
`>=`           |Supérieur ou égal à                    |`5 >= 4`
`in`           |Égal à l’un des éléments       |[voir ici](inoperator.md)
`!in`          |N’est égal à aucun des éléments   |[voir ici](inoperator.md)

**Commentaire concernant l’opérateur modulo**

Le modulo de deux nombres renvoie toujours à Kusto un "petit nombre non négatif".
Ainsi, le modulo de deux nombres, *N* %  &le; *D*, est tel que: 0 (*N* % *D*) &lt; abs (*D*).

Par exemple, la requête suivante :

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

Produit ce résultat :

|plusPlus  | moinsPlus  | plusMinus  | moinsMinus|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |