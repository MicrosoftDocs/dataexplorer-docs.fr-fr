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
ms.openlocfilehash: cafb52b7254041a18a9cae956ed338f45bc67a54
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818570"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Calcule le rang approximatif de la valeur dans un jeu, où le rang est exprimé sous la forme d’un pourcentage de la taille de l’ensemble.
Cette fonction peut être affichée comme inverse du centile.

**Syntaxe**

`percentrank_tdigest(`*TDigest* `,` *Expr*`)`

**Arguments**

* *TDigest*: expression générée par [TDigest ()](tdigest-aggfunction.md) ou [tdigest_merge ()](tdigest-merge-aggfunction.md).
* *Expr*: expression représentant une valeur à utiliser pour le calcul du rang en pourcentage.

**Retourne**

Rang en pourcentage de la valeur dans un DataSet.

**Conseils**

1) Le type du deuxième paramètre et le type des éléments dans le `tdigest` doivent être identiques.

2) Le premier paramètre doit être TDigest qui a été généré par [TDigest ()](tdigest-aggfunction.md) ou [tdigest_merge ()](tdigest-merge-aggfunction.md)

**Exemples**

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
