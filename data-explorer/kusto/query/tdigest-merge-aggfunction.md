---
title: tdigest_merge() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit tdigest_merge () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0b7de916dd53c19a49301c8048e2d8867d1b1249
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506390"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge)) (fonction d’agrégation)

Fusionne les résultats les plus tdigests dans l’ensemble du groupe. 

* Peut être utilisé uniquement dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md).

En savoir plus sur l’algorithme sous-jacent (T-Digest) et l’erreur estimée [ici](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Syntaxe**

résumer `tdigest_merge(` *Expr*`)`.

résumer `tdigest_merge(` *Expr* `)` - Un alias.

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

Les valeurs tdigestes fusionnées *d’Expr* à travers le groupe.
 

**Conseils**

1) Vous pouvez utiliser`percentile_tdigest()`la fonction [ ] (percentile-tdigestfunction.md).

2) Tous les tdigestes qui sont inclus dans le même groupe doivent être du même type.