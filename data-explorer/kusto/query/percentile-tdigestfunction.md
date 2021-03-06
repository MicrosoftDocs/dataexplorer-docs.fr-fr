---
title: percentile_tdigest ()-Azure Explorateur de données
description: Cet article décrit percentile_tdigest () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 7111f34fcd42e025b22960aa013310d7e4b672fd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252175"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

Calcule le résultat de centile à partir des `tdigest` résultats (générés par [tdigest ()](tdigest-aggfunction.md) ou [tdigest_merge ()](tdigest-merge-aggfunction.md))

## <a name="syntax"></a>Syntaxe

`percentile_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *typeLiteral*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *Percentile2*]... [ `,` *Centilen*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Tableau dynamique*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui a été générée par [`tdigest`](tdigest-aggfunction.md) ou [tdigest_merge ()](tdigest-merge-aggfunction.md).
* *Centile* est une constante double qui spécifie le centile.
* *typeLiteral*: littéral de type facultatif (par exemple, `typeof(long)` ). Si elle est fournie, le jeu de résultats sera de ce type. 
* *Tableau dynamique*: liste de centiles dans un tableau dynamique de nombres entiers ou à virgule flottante.

## <a name="returns"></a>Retours

Valeur centiles/percentilesw de chaque valeur dans *`Expr`* .

**Conseils**

* La fonction doit recevoir au moins un pour cent (et peut-être plus, voir la syntaxe ci-dessus : *Percentile1* [ `,` *Percentile2*]... [ `,` *Centilen*]) et le résultat est un tableau dynamique qui comprend les résultats. (tel que [`percentiles()`](percentiles-aggfunction.md) )
  
* Si un seul pourcentage a été fourni et que le type a été fourni également, le résultat sera une colonne du même type fourni avec les résultats de ce pourcentage. Dans ce cas, toutes les `tdigest` fonctions doivent être de ce type.

* Si *`Expr`* comprend `tdigest` des fonctions de types différents, ne fournissez pas le type. Le résultat sera de type dynamique. Consultez les exemples ci-dessous.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|62 millions|
|110 millions|
|1,2 million|
|250 000|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0, 0, 0]|
|[0, 0, 62000000]|
|[0, 0, 110000000]|
|[0, 0, 1200000]|
|[0, 0, 250000]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[62 millions]|
|["2007-12-20T11:30:00.0000000 Z"]|
|["2007-12-31T23:59:00.0000000 Z"]|
