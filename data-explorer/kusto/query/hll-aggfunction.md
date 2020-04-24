---
title: hll() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit hll() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: 52eac2984ed29bf8de21fb378a84b789015aa1c5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514125"
---
# <a name="hll-aggregation-function"></a>hll() (fonction d’agrégation)

Calcule les résultats intermédiaires de [dcount](dcount-aggfunction.md) à travers le groupe. 

* Peut être utilisé uniquement dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md).

Lisez sur [l’algorithme sous-jacent (*H*yper*L*og*L*og) et la précision d’estimation](dcount-aggfunction.md#estimation-accuracy).

**Syntaxe**

`summarize``hll(` *Expr* `,` [ *Précision*]`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 
* Si *Accuracy* est spécifié, détermine le compromis entre vitesse et précision.
    * `0` : calcul le moins précis, mais le plus rapide. Erreur de 1,6 %
    * `1`- le défaut, qui équilibre la précision et le temps de calcul; d’environ 0,8 % d’erreur.
    * `2`- calcul précis et lent; d’environ 0,4 % d’erreur.
    * `3`- calcul très précis et lent; environ 0,28% d’erreur.
    * `4`- calcul super précis et le plus lent; d’environ 0,2 % d’erreur.
    
**Retourne**

Les résultats intermédiaires d’un nombre distinct *d’Expr* dans l’ensemble du groupe.
 
**Conseils**

1) Vous pouvez utiliser la fonction d’agrégation [hll_merge](hll-merge-aggfunction.md) de fusionner plus d’un hll résultats intermédiaires (il fonctionne sur la sortie de hll seulement).

2) Vous pouvez utiliser la fonction [dcount_hll](dcount-hllfunction.md) qui calculera le dcount à partir de hll / hll_merge fonctions d’agrégation.

**Exemples**

```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|hll_DamageProperty|
|---|---|
|2007-09-18 20:00:00.0000000|[[1024,14],[-5473486921211236216,-6230876016761372746,3953448761157777955,4246796580750024372],[]]|
|2007-09-20 21:50:00.0000000|[[1024,14],[4835649640695509390],[]]|
|2007-09-29 08:10:00.0000000|[[1024,14],[4246796580750024372],[]]|
|2007-12-30 16:00:00.0000000|[[1024,14],[4246796580750024372,-8936707700542868125],[]]|