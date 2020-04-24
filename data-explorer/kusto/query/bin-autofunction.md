---
title: bin_auto() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit bin_auto() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebb214ae6a2676bf59a37e1e4e9cc3c085374bb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517831"
---
# <a name="bin_auto"></a>bin_auto()

Arrondit les valeurs jusqu’à un « bac » de taille fixe, avec le contrôle de la taille du bac et du point de départ fourni par une propriété de requête.

**Syntaxe**

`bin_auto` `(` *Expression* `)`

**Arguments**

* *Expression*: Expression scalaire d’un type numérique indiquant la valeur à arrondir.

**Propriétés de demande de client**

* `query_bin_auto_size`: Un littéral numérique indiquant la taille de chaque bac.
* `query_bin_auto_at`: Un littéral numérique indiquant une valeur `bin_auto(fixed_point)` == `fixed_point` *d’expression* qui est `fixed_point` un « point fixe » (c’est-à-dire une valeur pour laquelle .)

**Retourne**

Le multiple `query_bin_auto_at` le plus proche `query_bin_auto_at` de ci-dessous *Expression*, décalé de sorte que sera traduit en lui-même.

**Exemples**

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|Timestamp                    | count_|
|-----------------------------|-------|
|2017-01-01 00:05:00.0000000  | 60    |
|2017-01-01 01:05:00.0000000  | 56    |