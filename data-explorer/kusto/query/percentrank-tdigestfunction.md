---
title: percentrank_tdigest() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit percentrank_tdigest() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: fe356ddb2e6301bbb283d2e6aa59b5c98f8bf3fe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511184"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Calcule le rang approximatif de la valeur dans un ensemble où le rang est exprimé en pourcentage de la taille de l’ensemble. Cette fonction peut être considérée comme l’inverse du percentile.

**Syntaxe**

`percentrank_tdigest(`*Expr TDigest* `,` *Expr*`)`

**Arguments**

* *TDigest*: Expression qui a été générée par [tdigest()](tdigest-aggfunction.md) ou [tdigest_merge()](tdigest-merge-aggfunction.md).
* *Expr*: Expression représentant une valeur à utiliser pour le calcul du classement en pourcentage.

**Retourne**

Le pourcentage de valeur dans un jeu de données.

**Conseils**

1) Le type de deuxième paramètre et le type d’éléments dans le tdigest doit être le même.

2) Le premier paramètre doit être TDigest qui a été généré par [tdigest()](tdigest-aggfunction.md) ou [tdigest_merge()](tdigest-merge-aggfunction.md)

**Exemples**

Obtenir le percentrank_tdigest() de la propriété de dommages qui a évalué 4490 $ est de 85% :

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|Colonne1|
|---|
|85.0015237192293|


Utilisation percentile 85 sur la propriété de dommages devrait donner 4490$:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|