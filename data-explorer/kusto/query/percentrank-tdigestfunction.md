---
title: percentrank_tdigest ()-Azure Explorateur de données
description: Cet article décrit percentrank_tdigest () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 33eb35e51f403a3c0b7a2f030604b12c705221ea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346165"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Calcule le rang approximatif de la valeur dans un jeu, où le rang est exprimé sous la forme d’un pourcentage de la taille de l’ensemble.
Cette fonction peut être affichée comme inverse du centile.

## <a name="syntax"></a>Syntaxe

`percentrank_tdigest(`*TDigest* `,` *Expr*`)`

## <a name="arguments"></a>Arguments

* *TDigest*: expression générée par [TDigest ()](tdigest-aggfunction.md) ou [tdigest_merge ()](tdigest-merge-aggfunction.md).
* *Expr*: expression représentant une valeur à utiliser pour le calcul du rang en pourcentage.

## <a name="returns"></a>Retourne

Rang en pourcentage de la valeur dans un DataSet.

**Conseils**

1) Le type du deuxième paramètre et le type des éléments dans le `tdigest` doivent être identiques.

2) Le premier paramètre doit être TDigest qui a été généré par [TDigest ()](tdigest-aggfunction.md) ou [tdigest_merge ()](tdigest-merge-aggfunction.md)

## <a name="examples"></a>Exemples

L’obtention de la percentrank_tdigest () de la propriété dommages ayant la valeur $4490 est ~ 85% :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|Colonne1|
|---|
|85.0015237192293|


L’utilisation de centile 85 sur la propriété dommages doit indiquer $4490 :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|
