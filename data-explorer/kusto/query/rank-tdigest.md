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
ms.openlocfilehash: a849cd496d41ad473768b3f267639eaf8c467880
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741779"
---
# <a name="rank_tdigest"></a>rank_tdigest()

Calcule le rang approximatif de la valeur dans un jeu. Le rang de `v` la valeur dans `S` un jeu est défini en tant que `S` nombre de membres de qui sont `v`plus `S` petits ou égaux à `tdigest`, est représenté par son.

**Syntaxe**

`rank_tdigest(`*`TDigest`*`,` *`Expr`*`)`

**Arguments**

* *TDigest*: expression générée par [TDigest ()](tdigest-aggfunction.md) ou [tdigest_merge ()](tdigest-merge-aggfunction.md)
* *Expr*: expression représentant une valeur à utiliser pour le calcul du classement.

**Retourne**

Valeur de classement foreach dans un jeu de données.

**Conseils**

1) Les valeurs dont vous souhaitez obtenir le rang doivent être du même type que le `tdigest`.

**Exemples**

Dans une liste triée (1-1000), le rang de 685 est son index :

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

Cette requête calcule le rang de la valeur $4490 sur les coûts des propriétés de tous les dommages :

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

Obtention du pourcentage estimé du rang (en divisant par la taille définie) :

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


Le centile 85 des propriétés de dommages coûte $4490 :

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


