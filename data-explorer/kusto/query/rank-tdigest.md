---
title: rank_tdigest() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit rank_tdigest() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: ea24213b0ca673c39f399c3a12cc54cd7d7f47d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510538"
---
# <a name="rank_tdigest"></a>rank_tdigest()

Calcule le rang approximatif de la valeur dans un ensemble. Le rang `v` de `S` valeur dans un ensemble `S` est défini comme `v` `S` le nombre de membres de ce qui sont plus petits ou égaux à , est représenté par son tdigest.

**Syntaxe**

`rank_tdigest(`*Expr TDigest* `,` *Expr*`)`

**Arguments**

* *TDigest*: Expression qui a été générée par [tdigest()](tdigest-aggfunction.md) ou [tdigest_merge()](tdigest-merge-aggfunction.md)
* *Expr*: Expression représentant une valeur à utiliser pour le calcul du classement.

**Retourne**

Le rang est avant la valeur dans un ensemble de données.

**Conseils**

1) Les valeurs que vous voulez obtenir son rang doit être du même type que le tdigest.

**Exemples**

Dans une liste triée (1-1000), le rang de 685 est son indice:

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

Cette requête calcule le rang de valeur 4490$ sur tous les coûts des propriétés de dommages :

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

Obtenir le pourcentage estimatif du rang (en divisant par la taille de l’ensemble) :

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


Le percentile 85 des coûts de propriétés de dommages est de 4490$ :

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


