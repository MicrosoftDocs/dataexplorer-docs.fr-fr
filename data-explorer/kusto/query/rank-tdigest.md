---
title: rank_tdigest ()-Azure Explorateur de données
description: Cet article décrit rank_tdigest () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 143257a586bb951caeb116882551e55f89c8636e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345876"
---
# <a name="rank_tdigest"></a>rank_tdigest()

Calcule le rang approximatif de la valeur dans un jeu. Le rang de `v` la valeur dans un jeu `S` est défini en tant que nombre de membres de `S` qui sont plus petits ou égaux à `v` , `S` est représenté par son `tdigest` .

## <a name="syntax"></a>Syntaxe

`rank_tdigest(`*`TDigest`*`,` *`Expr`*`)`

## <a name="arguments"></a>Arguments

* *TDigest*: expression générée par [TDigest ()](tdigest-aggfunction.md) ou [tdigest_merge ()](tdigest-merge-aggfunction.md)
* *Expr*: expression représentant une valeur à utiliser pour le calcul du classement.

## <a name="returns"></a>Retourne

Valeur de classement foreach dans un jeu de données.

**Conseils**

1) Les valeurs dont vous souhaitez obtenir le rang doivent être du même type que le `tdigest` .

## <a name="examples"></a>Exemples

Dans une liste triée (1-1000), le rang de 685 est son index :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

Cette requête calcule le rang de la valeur $4490 sur les coûts des propriétés de tous les dommages :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

Obtention du pourcentage estimé du rang (en divisant par la taille définie) :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


Le centile 85 des propriétés de dommages coûte $4490 :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


