---
title: percentile_tdigest() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit percentile_tdigest() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 655a0693b8e04b1230f6b9e8fe247bd2d77b7ac6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511235"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

Calcule le résultat percentile des résultats tdigest (qui a été généré par [tdigest()](tdigest-aggfunction.md) ou [tdigest_merge))](tdigest-merge-aggfunction.md))

**Syntaxe**

`percentile_tdigest(`*Expr* `,` *Percentile1* [`,` *typeLiteral*]`)`

`percentiles_array_tdigest(`*Expr* `,` *Percentile1* [`,` *Percentile2*] ... [`,` *Percentilen*]`)`

`percentiles_array_tdigest(`*Tableau* `,` *dynamique* Expr`)`

**Arguments**

* *Expr*: Expression qui a été générée par [tdigest](tdigest-aggfunction.md) ou [tdigest_merge()](tdigest-merge-aggfunction.md).
* *Percentile* est une double constante qui spécifie le percentile.
* *typeLiteral*: Un type facultatif littéral (p. ex., `typeof(long)`). Si fourni, l’ensemble de résultat sera de ce type. 
* *Tableau dynamique*: liste des percentiles dans un tableau dynamique de nombres de points integers ou flottants

**Retourne**

La valeur percentiles/percentilesw de chaque valeur dans *Expr*.

**Conseils**

1) La fonction doit recevoir au moins un pour cent (et peut-être plus, voir la syntaxe ci-dessus: *Percentile1* [`,` *Percentile2*] ... [`,` *Percentilen*]) et le résultat sera un tableau dynamique qui comprend les résultats. (comme) [`percentiles()`](percentiles-aggfunction.md)
  
2) si seulement un pour cent a été fourni et le type a été fourni aussi alors le résultat sera une colonne du même type fourni avec les résultats de ce pourcentage (tous les tdigestes doivent être de ce type dans ce cas).

3) si *Expr* inclut des tdigestes de différents types, alors ne fournissez pas le type et le résultat sera de type dynamique. (voir les exemples ci-dessous).

**Exemples**

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|62000000|
|110000000|
|1200000|
|250 000|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0,0,0]|
|[0,0,62000000]|
|[0,0,110000000]|
|[0,0,1200000]|
|[0,0,250000]|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[62000000]|
|["2007-12-20T11:30:00.0000000Z"]|
|["2007-12-31T23:59:00.0000000Z"]|